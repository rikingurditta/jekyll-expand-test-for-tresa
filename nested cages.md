# Nested Cages

- many tasks in geometry/simulations benefit from multiresolution hierarchies
- important that coarser layers strictly encage finer layers
  - that way layers are *nested*
  - existing techniques do not provide sufficient control over quality of output surfaces while maintaining strict nesting
    - existing techniques such as surface mesh decimation, voxelization, contouring distance level sets
- paper enables use of application-specific decimation and quality metrics
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
  - method is agnostic to decemation scheme used to create $\hat{C}$
