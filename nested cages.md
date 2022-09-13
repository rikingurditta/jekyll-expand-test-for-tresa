$$
\newcommand{\ds}{\displaystyle}
\newcommand{\Fbar}{\bar F}
\newcommand{\fbarbold}{\bar{\mathbf f}}
\newcommand{\Fbarbold}{\bar{\mathbf F}}
\newcommand{\pbarbold}{\bar{\mathbf p}}
\newcommand{\Chat}{\hat C}
$$

# Nested Cages

- many tasks in geometry/simulations benefit from multiresolution hierarchies
- important that coarser layers strictly encage finer layers
  - that way layers are *nested*
  - existing techniques do not provide sufficient control over quality of output surfaces while maintaining strict nesting
    - existing techniques include surface mesh decimation, voxelization, contouring distance level sets
- paper enables use of application-specific decimation and quality metrics
  - any decimation algorithm can be used
  - method constructs each next-coarsest level of hierarchhy using sequence of decimation, flow, contact-aware optimization steps
- will use $C$ to denote cage (coarse mesh) around original fine mesh $F$

## Introduction

- a powerful acceleration technique is decomposing high res mesh into hierarchy of increasingly coarse approximations (cages)
  - multigrid FEM techniques efficiently solve Eulerian PDEs on very fine meshes by moving up and down hierarchy
  - physics simulations use coarse cages by interpolating their deformations onto high resolution geometries
  - in interactive animation, artists specify large scale deformations by adjusting low-dimensional cage
  - in collision detection, conservative culling reduces computation time
- straightforward approach is to use applicaiton-specific decimation algorithm
  - usually this results in a coarse mesh that intersects the original (generally undesirable)
    - bad for most transferring pose algs
    - not useful for collision detection
- desired cage $C$ is nested around $F$ - surrounds $F$ without intersecting
  - for watertight meshes, we want:
    - $F$ contained in interior of $C$
    - for every loop on $C$, there is a map to a loop on $F$ that is completely within $C$
  - method can be generalized from watertight meshes to polygon soups
- nested cage algorithm - given 
- paper presents practical algorithm for solving nested cage problem on typical meshes
  - method is agnostic to decimation scheme used to create $\Chat$

## Method

Input:

- $k+1$ potentially overlapping meshes $\hat{M_0}$, $\hat{M_1}$, $...$, $\hat{M_k}$
  - a mesh $M_i$ is a list of initial vertex positions $\hat{\mathbf{M}_i}$ and a list of triangle indices $\mathbf{T}_i$
- typically $\hat{M_0}$ is the high res original mesh, and each mesh after is coarser than the last

Output:

- new vertex positions $\mathbf{M}_1$, $...$, $\mathbf{M}_k$ so that for $i = 1 ... k$ each $M_i = (\mathbf{M}_i, \mathbf{T}_i)$ is a deformed mesh that nests $M_{i-1}$

Algorithm:

```python
def nested_cages(M_hat[0..k], Energy):
    M[0..k] = []                            # output list
    M[0] = M_hat[0]                         # finest mesh is unchanged
    for i in 1..k:
        F = M[i-1]
        C_hat = M_hat[i]
        H = Shrink(C_hat, F)                # H = shrink F until it fits inside C_hat
        M[i] = Reinflate(H, C_hat, Energy)  # M[i] = C_hat after inflating H inside it
                                            #        while minimizing energy
```

### Flow

Let $\Phi( \Fbarbold )$ denote the total signed distance from the vertices $\Fbarbold = \{ \pbarbold : \pbarbold \text{ is  a vertex of } \Fbar\}$ to the mesh $\Chat$:

$$
\Phi(\Fbarbold) = \int_\Fbarbold s(\pbarbold) d(\pbarbold) \ \text{d}A
$$

where $s(\pbarbold)$ is the *sign* of $\pbarbold$ inside $\Chat$, i.e.

$$
s(\pbarbold) = \begin{cases}
1 & \text{if } \pbarbold \text{ is outside } \Chat \\
0 & \text{if } \pbarbold \text{ is on the surface of } \Chat \\
-1 & \text{if } \pbarbold \text{ is inside } \Chat
\end{cases}
$$

and $d(\pbarbold)$ is the unsigned distance from $\pbarbold$ to the closest point on $\Chat$, so $s(\pbarbold)d(\pbarbold)$ is the signed distance from $\pbarbold$ to the closest point on $\Chat$.

We want to flow our fine mesh into our coarse mesh, aka flow so that the total signed distance becomes as negative as possible. So for each vertex $\fbarbold \in \Fbarbold$, the flow direction should be

$$
\frac{\partial \fbarbold}{\partial t} = -\nabla_\Fbarbold \Phi(\Fbarbold)
$$

This flow will move points toward the *medial axis* of $\Chat$. We flow in this direction until $\Fbarbold$ is entirely within $\Chat$.

### Reinflation

- after the flow step, $\Fbarbold$ should be completely within $\Chat$

- in the inflation step, we inflate back $\Fbarbold$ back to $\mathbf F$ by reversing the flow

- we get the "virtual velocity" $\mathbf U_F(t)$ at each step by taking finite differences

$$
\mathbf U_F(t) = \frac{\Fbarbold(t + \Delta t) - \Fbarbold(t)}{\Delta t}
$$

- we move the vertices of $C$ to respond to "collisions" during this step

We define the energy of the coarse mesh $E(F, \Chat, C)$, and try to make our updates $\mathbf C(t + \Delta t)$ in a way that minimizes the energy subject to constraints that keep the system intersection-free. So we can state our problem as:

$$
\underset{\mathbf C(t + \Delta t)}{\text{min}} E(F, \Chat, \mathbf C(t + \Delta t)) \\
\text{ subject to:} \\
\forall s \in [t, t + \Delta t], \
\begin{array}{l}
C(s) \text{ does not intersect itself} \\
C(s) \text{ does not intersect } \Fbar(s)
\end{array}
$$

This means we are solving a *continuous time* problem. We can use any contact resolution system for this