[![Build Status](https://github.com/jrouwe/JoltPhysics/actions/workflows/build.yml/badge.svg)](https://github.com/jrouwe/JoltPhysics/actions/)

# Jolt Physics Library

A multi core friendly rigid body physics and collision detection library suitable for games and VR applications.

## Design Considerations

So why create yet another physics engine? First of all, this has been a personal learning project and secondly I wanted to address some issues that I had with existing physics engines:

* In games we usually need to do many more things than to simulate the physics world and we need to do this across multiple threads. We therefore place a lot of emphasis on concurrently accessing the physics simulation data outside of the main physics simulation update:
	* Sections of the world can be loaded / unloaded in the background. A batch of physics bodies can be prepared on a background thread without locking or affecting the physics simulation and then inserted into the world all at once with a minimal impact on performance.
	* Collision queries can run in parallel with other operations like insertion / removal of bodies. The query code is guaranteed to see a body in a consistent state, but when a body is changed during a collision query there is no guarantee if the change is visible to the query or not. If a thread modifies the position of a body and then does a collision query, it will immediately see the updated state (this is often a problem when working with a read version and a write version of the world).
	* It is also possible to run collision queries in parallel to the main physics simulation by doing the broad phase query before the simulation step. This way, long running processes (like navigation mesh generation) can be spread out across multiple frames while still running the physics simulation every frame.
* One of the main sources of performance problems we found was waking up too many bodies while loading / unloading content. Therefore, bodies will not automatically wake up when created and neighboring bodies will not be woken up when bodies are removed. This can be triggered manually if desired.
* The simulation runs deterministically, so you could replicate a simulation to a remote client by merely replicating the inputs to the simulation.
* The simulation of this physics engine tries to simulate behavior of rigid bodies in the real world but makes approximations in the simulation so should mainly be used for games or VR simulations.

For more information see the [Architecture](Docs/Architecture.md) section.

## Features

* Simulation of rigid bodies of various shapes using continous collision detection:
	* Sphere.
	* Box.
	* Capsule.
	* Tapered-capsule.
	* Cylinder.
	* Convex hull.
	* Compound.
	* Mesh (triangle).
	* Terrain (height field).
* Simulation of constraints between bodies:
	* Fixed.
	* Point.
	* Distance (including springs).
	* Hinge.
	* Slider (also called prismatic).
	* Cone.
	* Smooth spline paths.
	* Swing-twist (for humanoid shoulders).
	* 6 DOF.
* Motors to drive the constraints.
* Collision detection:
	* Casting rays.
	* Testing shapes vs shapes.
	* Casting a shape vs another shape.
	* Broadphase only tests for quickly determining which objects may intersect.
* Sensors (trigger volumes).
* Animated ragdolls:
	* Hard keying (kinematic only rigid bodies).
	* Soft keying (setting velocities on dynamic rigid bodies).
	* Driving constraint motors to an animated pose.
* Game character simulation (capsule), although many games may want to implement characters just using collision tests for more control over the simulation.
* Vehicle simulation of wheeled and tracked vehicles.
* Water buoyancy calculations.

## Supported Platforms

* Windows (VS2019) x64
* Linux (tested on Ubuntu 20.04) x64/ARM64
* Android (tested on Android 10) x64/ARM64
* Platform Blue (a popular game console) x64

## Required CPU features

* On x86 the minimal requirements are SSE4.2 but the library can be compiled using FP16C, AVX or AVX2.
* On ARM64 the library requires NEON with FP16 support.

## Compiling

* The library has been tested to compile with Cl (Visual Studio) and Clang 10+.
* It uses C++17 and only depends on the standard template library.
* It doesn't make use of compiler generated RTTI or exceptions.
* If you want to run on Platform Blue you'll need to provide your own build environment and PlatformBlue.h file due to NDA requirements (see Core.h for further info).

For build instructions go to the [Build](Build/README.md) section.

## License

The project is distributed under the [MIT license](LICENSE).