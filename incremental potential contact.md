# Incremental Potential Contact: Intersection- and Inversion-free Large-Deformation Dynamics

- efficiently time-stepping accurate and consistent simulations of real-world contacting elastic objects remains difficult
- IPC is new model and algorithm for variationally solving implicitly time-stepped non-linear elastodynamics
- solves contact problems that are:
  - intersection-free
  - inversion-free
  - efficient
    - comparable to/faster than available methods (which lack both convergence and feasibility)
  - accurate

## Introduction

- contact is difficult due to intertwined physical and geometric factors, especially in the presence of friction and nonlinear elasticity
  - real-world contact and friction forces are discontinuous - makes for stiff time-stepping problems
  - small violations of exact contact constraints (which are nonconvex) can lead to very bad geometric configurations (impossible to untangle)
    - also often lead to extreme deformations resulting in element inversions
  - friction introduces challenges with asymmetric force coupling and rapid switching between sliding and sticking

Goals:

- achieve very high robustness
  - i.e. no catastrophic failures or stagnation
  - even for challenging elastodynamic contact problems with friction
  - should be independent of user-controllable accuracy in time-stepping, spacial discretization, contact resolution
  - still maintain efficiency
- ensure that all accuracies are efficiently attainable when required

Specifications:

- solver is constructed for mesh-based discretizations of nonlinear volumetric elastodynamic problems
- solver supports
  - large nonlinear deformations
  - implicit time-stepping with contact
  - friction
  - boundaries of arbitrary codimension (points, curves, surfaces, volumes)
- physics can be approximated arbitrarily coarsely but geometric constraints (no intersections, no inversions) are maintained exactly

Approach:

- key element is formulation of contact problem and customized numerical method to solve
- exact constraint formulation in terms of unsigned distance function, and rate-based maximal dissipation for friction
- for each timestep, solve discreete nonlinear contact problem with given tolerance
  - use smoothed barrier method
    - almost everywehre $C^2$ function of unsigned distances
      - $C^1$ for measure-zero set of configurations
      - makes it possible to use newton-type optimizatio methods
    - support is restricted to small part of configuration space close to configuration with contact
      - additional contact forces are applied highly locally
      - only a small set of terms of barrier function need to be computed explicitly during optimization
  - ensure that solution is intersection-free at all intermediate steps
    - line search has filtered continuous collision detection
  - use comparably smoothed arbitrarily close approximation to static friction (so no explicit Coulomb constant constraint needed)
    - cast friction forces at every timestep in a dissipative potential form
    - friction is resolved in same solver

## Method

### Contact model

- $x$ is the positions of all $n$ nodes (so $\dot x$ is their velocities)
- $M$ is the FEM mass matrix for the nodes
- $\Psi(x)$ is the deformation energy of the material
- $f_e$ is the external forces on the nodes
- $f_d$ is the dissipative forces, i.e. frictional forces
- $T$ is the total time
- $\mathcal A$ is the set of admissible trajectories

We consider the energy

$$
S(x) = \int_0^T \left( \frac{1}{2} \dot x^T M \dot x + \Psi(x) + x^T \left( f_e + f_d \right) \right) dt
$$
