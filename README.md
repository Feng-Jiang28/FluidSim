# FluidSim

Phase 1: 
Starting from a famous paper "Stable Fuilds" by Jos Stam, we used the theory given by the paper, we are able to generate a simple version of fluid simumation. 

![Screenshot 2023-11-29 at 14 09 57](https://github.com/Feng-Jiang28/FluidSim/assets/106386742/d5414692-a040-4982-a55d-c4777fc17dee)

Basics: 
Fluid simulation is based on Navier-Stokes equeations by treating the fluid as a grid of interacting cells or boxes.
Each cell has properties like velocity and density. For practicality, simulations use a limited number of cells and interactions due to computational limits.
<img width="384" alt="image" src="https://github.com/Feng-Jiang28/FluidSim/assets/106386742/43fe3ed4-6d7b-4792-9e80-c11bbf938158">

The focus is on incompressible fluids (like water) rather than compressible ones (like air), as they are easier to simulate with constant density and pressure. 
To visualize fluid movement, a dye is added, and its density variation helps in observing the motion.

Data Structures(C)
```
struct FluidCube {
    int size;
    float dt;
    float diff;
    float visc;
    
    float *s;
    float *density;
    
    float *Vx;
    float *Vy;
    float *Vz;

    float *Vx0;
    float *Vy0;
    float *Vz0;
};
typedef struct FluidCube FluidCube;
```
Since this just initializes a motionless cube with no dye, we need a way both to add some dye:
```
void FluidCubeAddDensity(FluidCube *cube, int x, int y, int z, float amount)
{
    int N = cube->size;
    cube->density[IX(x, y, z)] += amount;
}
```
And to add some velocity:
```
void FluidCubeAddVelocity(FluidCube *cube, int x, int y, int z, float amountX, float amountY, float amountZ)
{
    int N = cube->size;
    int index = IX(x, y, z);
    
    cube->Vx[index] += amountX;
    cube->Vy[index] += amountY;
    cube->Vz[index] += amountZ;
}
```

Simulation Outline
There are three main operations that we'll use to perform a simulation step.

Diffuse: This operation simulates the spreading out of substances in the fluid, like dye in water. It applies to both the dye and the fluid's velocities, representing how they naturally disperse over time.

Project: This step ensures the simulation adheres to the principle of incompressibility. It adjusts the fluid in each cell so that the volume remains constant, correcting any imbalances caused by other operations.

Advect: This process simulates the movement of the fluid and the dye within it, driven by the velocities of each cell. It affects both the dye and the fluid's motion.

Additionally, there are two subroutines:

set_bnd (Set Bounds): This subroutine acts as a boundary condition, preventing the fluid from "leaking" out of the simulated area. It does this by mirroring the velocities at the edges, creating a wall-like effect.

lin_solve (Linear Solver): A function used in both the diffusion and projection steps. It solves linear equations related to these processes, although the exact workings might be complex and not fully understood by the implementer.

![ezgif com-video-to-gif](https://github.com/Feng-Jiang28/FluidSim/assets/106386742/72acbe95-6bef-45e9-b595-e3c38e9e9add)

The implementation of fluid simulation as described, while effective for demonstrating the principles of fluid dynamics in a 3D environment, does have some computational limitations.
1. High Computational Load:
Fluid simulation involves complex calculations for each cell in the fluid grid for every simulation step. This includes solving differential equations and updating the properties (like velocity and density) of each cell based on its interactions with neighboring cells.
2. Numerical Methods:
The implementation uses numerical methods (like the linear solver) that require iterative calculations. These methods can be computationally expensive, especially for high-resolution simulations.
3. Real-Time Performance:
Achieving real-time performance in fluid simulations is challenging, particularly for simulations with a large number of cells or high complexity.
4. Serial Computation:
The basic implementation as described is serial in nature, meaning each calculation is done one after the other. This approach doesn't efficiently utilize modern multi-core processors or parallel processing capabilities of GPUs.

Phase 2:

In phase 2, we are trying to implement a fluid simulation a CPU with OpenMP and GPU with CUDA, and compared the performance of the two implementations. 
OpenMP (Open Multi-Processing):

It allows the fluid simulation to utilize multiple cores of a CPU efficiently.
By dividing the simulation grid and processing multiple parts simultaneously, OpenMP can significantly reduce the time required for each simulation step.

CUDA (Compute Unified Device Architecture):

GPUs are highly efficient at handling parallel tasks due to their large number of cores designed for handling graphics operations.
Fluid simulations can benefit greatly from CUDA as it allows distributing the computation across thousands of small, efficient cores in the GPU, drastically speeding up the simulation process.


