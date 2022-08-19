# Progressive Cloth Simulation

- start by manipulating coarse simulation to get desired draping pose
- PCS guarantees self-consistent deformations for finer and finer meshes until fully converged simulation
  - contact behaviour of solution is consistent with coarser levels
- allows for interactive and predictive way to simulate high fidelity cloth

## Intro

- speed and fidelity is always a tradeoff in simulations
- both are advancing
  - high fidelity methods are getting more realistic but remain slow
  - high speed methods are advancing, now reaching real time speeds for high resolution meshes
- resolution =/= fidelity
  - high speeds may be achieved by simplifications/limitations to the cloth models
- PCS
  - coarse mesh previewing cloth sim with frictional contact and (when required) strain limiting
  - its output is progressively improved resolution by resoltion
  - has consistent shape and folds across levels
  - solution *converges* as opposed to maintaining consistency through constraints
  - built upon C-IPC (Codimensional Incremental Potential Contact) cloth model
- useful for exploring changes to paterial parameters, boundary conditions, placement

## Related work

- designing with coarse then re-running at higher resolution is not necessarily a good strategy with other cloth models

## Formulation

### Goals

1. high quality (convergent, non-interpenetrating, artifact free) fine resolution simulations of drape geometries (supporting full range of material parameters)
2. coarse-resolution mesh providing efficient previewing (that is consistent with converged final configuration)
3. allow progressive and consistent resolution improvement of the solution (with increasing cost), starting from coarse preview mesh (2) annd ending at high quality mesh (1)

### Hierarchy

- for each simulation, construct a nested triangle mesh hierarchy
- notation:
  - $l \in [0, L]$ denotes level, use as subscript
  - $n_l \in \N$ is number of nodes
  - $x_l \in \R^{3n_l}$ is deformed node positions vector
  - $\overline{x_l} \in \R^{3n_l}$ is rest node positions vector
  - $T_l$ is triangulation
  - $P^l_{l+1}$ is prolongation operator to "project" $x_l$ to next level $\R^{3n_{l+1}$
  - $P^l$ is chained prolongation operator to "project$ $x_l$ to fine mesh space, aka final level $\R^{3n_L}$
  - $u$ is material and boundary conditions
- with this scheme, $x_0$ is is deformed positions of coarsest mesh, $x_L$ is deformed positions of finest mesh (target)

### Incremental potential problems

## Progressive simulations

- need sequentially improving hierarchy of self-consistent simulation solutions

2 solver phases:

- **Refinement**: $x_l^t \rightarrow x_{l+1}^t$ (given fixed set of conditions $u_t$)
- **Preview**: $x_l^t \rightarrow x_l^{t+1}$ (over possibly varying u)

### Coarse-level energies

- at each coarse level $l < L$, we construct mixed resolution objective $F_l$ as a *proxy energy* for finest-level potential energy
- $F_l(x_l) = C_l(x_l) + G(P^l x_l)$
  - $C_l$ is barrier-based potential energies, enforcing contact and strain-limit feasibility
  - $G$ is shell membrane and bending potential energies
    - $G$ is based on finest mesh resolution, using $P^l$ to project $x_l$ to level $L$

## Algorithm and implementation

### User in the loop interaction

- prototype tool created that allows interactive cotnrol of the scene using handles and collision geometries, as well as updates to material parameters

## Evaluation

## Limitations and conclusion

- method designed for cloth
  - extending to shells may be complicated because it is non-trivial to design hierarchies and prolongation operators on curved domains
  - similar issues when extending to volumetric solids
- 2D domains are completely determined by coarse geometry
  - real world cloth often has fine-scale domain boundaries and elaborate seam constraints
- only handles elasticity, not plasticity
- in real dynamics simulation, momentum keeps updates between timesteps small (?)
