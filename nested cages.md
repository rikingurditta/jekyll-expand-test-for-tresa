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
  - method is agnostic to decimation scheme used to create $\hat{C}$

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