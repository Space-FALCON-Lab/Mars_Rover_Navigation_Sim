# Mars Rover Navigation Simulation

This repository combines a Unity rover scene with a ROS 2 workspace for
experimental mapping and navigation. Unity supplies simulated measurements and
receives velocity commands. ROS 2 builds an OctoMap, projects it into a local
costmap and runs a custom A* planner.

Start here to obtain the paired projects. Continue with the
[ROS workspace guide](https://github.com/Space-FALCON-Lab/mars_rover_ros2_ws#build-and-run)
for the build, launch and connection checks. This is a simulation prototype;
the planner's current limitations are listed in that guide.

## What is in the checkout

| Directory | Purpose |
| --- | --- |
| `mars_rover_unity_sim/` | Unity scene, rover model, simulated sensors and the ROS TCP Connector package entry |
| `mars_rover_ros2_ws/` | ROS TCP Endpoint, mapping, costmap projection, A* planning and RViz configuration |

Both directories are Git submodules. Their recorded commits define the pair
used by this checkout; an empty directory means the submodule has not been
initialized. The current submodule URLs in [.gitmodules](.gitmodules) point to
repositories outside the Space-FALCON-Lab organization, separate from this
wrapper repository.

## Before you start

Prepare a ROS 2 Humble environment with Python 3, colcon and rosdep, following
the [ROS installation guide](https://docs.ros.org/en/humble/Installation.html).
The commands in the workspace guide use Bash on Ubuntu.

Install Git LFS before obtaining the Unity assets. The pinned Unity project
records **Unity 6000.0.43f1** in `ProjectSettings/ProjectVersion.txt`; open it
with that editor version through Unity Hub. The older recommendation to use
Unity 2022 does not describe this pinned project.

## Get both projects

In a terminal, choose the parent directory for your checkout, then run:

```bash
git lfs install
git clone --recurse-submodules https://github.com/Space-FALCON-Lab/Mars_Rover_Navigation_Sim.git
cd Mars_Rover_Navigation_Sim
git submodule status
git -C mars_rover_unity_sim lfs pull
```

For an existing checkout, initialize its recorded submodules with:

```bash
git submodule update --init --recursive
```

These commands use the recorded commits. Do not update the submodules to their
latest branches while reproducing this pair.

## Build and connect the simulation

1. Build `mars_rover_ros2_ws/` using the
   [workspace instructions](https://github.com/Space-FALCON-Lab/mars_rover_ros2_ws#build-and-run).
   That guide starts the ROS TCP Endpoint and launches
   `mars_rover_sim bringup_sim.launch.py`.
2. Add `mars_rover_unity_sim/` to Unity Hub and open
   `Assets/OutdoorsScene.unity`. Let Unity resolve the packages recorded in
   `Packages/manifest.json`.
3. In the Unity ROS connection settings, select ROS 2 and the address of the
   computer running the ROS TCP Endpoint, on port `10000`. If Unity and ROS run
   on different computers, use the ROS computer's reachable address.
4. Enter Play mode. Use the workspace guide's topic and transform checks to
   confirm that measurements arrive before interpreting the planned path.

The supplied scripts publish `/pointcloud`, `/pose`, `/clock` and `/tf`, and
subscribe to `/cmd_vel`. Mapping and planning also require the scene's transform tree,
including `map` to `base_link` and the LiDAR frame. Source scripts alone do not
confirm that every component is attached and configured in a particular scene.

## What to inspect first

Look for a point cloud in RViz, then a costmap on `/costmap_2d` and a path on
`/astar_path`. The current planner chooses its goal from a fixed offset in its
Python source; RViz's Nav2 goal tool is not its goal input. Costmap thresholds
and planner behavior are described in the workspace guide so you can decide
what to change for your experiment.
