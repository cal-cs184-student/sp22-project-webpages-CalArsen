## Milk Simulation

## **Summary:**

For our final project, we will be doing Milk simulation because we saw that the liquid simulation from the previous semester is very interesting and cool; rather than building a water simulation, we wanted to do it on a different type of liquid. The coding section will consist of a particle-based physics simulation of milk in motion, in some scene.  Our teammates are **Johnson Nguy**, **Dequan Zhang**, **Arsen Ter-Avakian**, and **Andy Jiang**.

## **Problem Description:**

Since we are trying to build a milk simulation, we need to stimulate by following the physics and properties of milk. Thus, we would need to understand the physics of liquid and the different properties of liquid. The problem that we are trying to solve is that, we need to implement the collisions of between particles and the collision between particles and the external objects such as a plane or a cup, to accurately emulate the motions of milk. The challenge arises in coding such problems in the first place, as without certain optimizations in place, this sort of thing can take an inordinate amount of real time to simulate due to the sheer scope of particles that are present in a given scene. We need to come up with something to reduce the computational strain and work the computer inherits as a result.

We believe that we can use a similar approach to our cloth simulation from project 4 where we create a particle system that can model the forces acting between sets of particles over some increment of time. In fact, if we can come up with some system similar to the spacial map that worked for self-collisions in the cloth simulator, we may be able to speed up the process of self-collisions in a fluid simulator as well. Otherwise, the general idea is just that, to take some of the implementation ideas that work for particle-based cloth simulations, and tweak them to work for a particle system composed of some other fluid’s particles, such as milk. Of course, to realistically do this, we would also need to research fluid simulations implemented by other people that have studies and articles posted about online, so as not to fall to get stuck on anything that requires more complexity than that which was seen in Project 4.

## **Goals and Deliverables:**

What we plan to deliver with this project mainly culminates in the closest possible physics simulation of milk to real milk in real time. As such, the deliverables of this project will be a showcase of our milk physics in different situations, such as milk falling from rest at a certain height, different amounts of milk falling from varied heights, a wave of milk coming from one other direction, etc. The idea here is that we simulate realistic milk as it would act in the real world, were it in real versions of the scenes that we will have built. We think that, given the advancements in fluid simulations that others have published (one can take a look at the resources section of this proposal) we can definitely achieve something like this in controlled environments. If we define a scene in a certain way, we can get results that most showcase the strengths of our physics model. Again, we can borrow some of the information from project 4 in the form of GLSL shaders, that can also take care of the visual look of the milk in our deliverable content.

Using similar methods as seen in previous projects, we can also provide graphs and real time measurements of the simulation so as to glean whether our code runs our model in an efficient manner or not. That is, we can use stopwatch and timer functions and headers to time the simulation function itself. This will be a great way of judging the performance of our project. Visual queues from the simulation deliverables will be ways of determining the quality of the simulation as a realistic depiction of milk in action. This is what we hope to deliver with this project, a simulation that can move particles accurately, as they largely collide with themselves, and in a timely fashion, as the simulation should not take longer than a minute to run smoothly.

## **Schedule:**

The organized plan we will execute shall contain the following tasks: to create some data structure to represent the particles in the system, to model external forces and collisions, as they would change a particle’s position, to model self-colliding forces within the fluid volume, and to code up shaders for the milk itself. Although this only comes out to four tasks, these shall take quite some time to complete correctly. We hope to finish each of these tasks every week to make it on time. The first task, the data structure, will likely consist of subtasks of finding the structure that makes the most sense to use with our online resources, and building it as the base for the rest of the project. It probably won’t have many individual subtasks as it will be building a solid single data structure, unless more elements have to be included in the system, such as the cloth simulator requiring springs as well as point masses. Modeling external forces shouldn’t have many subtasks either, as gravity is the only real force we will be putting in the system. This is why the second task lumps in external forces as well as collisions, because the forces won’t be anything crazy, and the collisions will be with simple stuff, like planes. As such the second task’s subtasks can be considered to be these two. The third task, the self-collisions, will be in subtasks that divy up the particles into groups, similar to the spatial map in project 4, and compute and apply correction vectors that update particle positions. Finally, when the shaders are being created, the subtasks will be to come up with some fluid reflection lambertian terms for a bsdf function, the fluid diffuse reflection term (as milk is opaque), and a refraction term as it is a liquid.

## **Resources:**

We will use the following resources and computing platforms to complete the project.
- Computing platform/IDE/Software: Visual Studio Code, GitHub
- Hardware: MacBook Pro
- Operating System: Mac OS
- Reference Codes: We will use all the previous project codes as reference for concepts and ideas to implement our project.
- Video References:
  - [But How DO Fluid Simulations Work?](https://www.youtube.com/watch?v=qsYE1wMEMPA)
  - [Coding Challenge #132: Fluid Simulation](https://www.youtube.com/watch?v=alhpH6ECFvQ)
  - [Beautiful Fluid Simulations...In Just 40 Seconds! 🤯](https://www.youtube.com/watch?v=LtyvS7NYonw)
  - [Finally, A Blazing Fast Fluid Simulator! 🌊](https://www.youtube.com/watch?v=i4KWiq3guRU)
- Online Articles/Books:
  - [FLUID SIMULATION - UBC Computer Science](https://www.cs.ubc.ca/~rbridson/fluidsimulation/fluids_notes.pdf)
  - [Fluid Simulation for Computer Graphics, 2nd Edition](https://www.oreilly.com/library/view/fluid-simulation-for/9781482232844/)
  - [Physically-Based Fluid Simulation for Computer Graphics](https://www.digipen.edu/sites/default/files/public/docs/theses/brian-trevethan-digipen-master-of-science-in-computer-science-thesis-physically-based-fluid-simulation-for-computer-graphics.pdf)
