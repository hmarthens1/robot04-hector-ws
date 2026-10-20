# robot04-hector-ws

The colcon workspace for **Hector SLAM on the PC** (or the Pi) for robot04 and the other
robots of [rpi5-ros2-rrclite](https://hmarthens1.github.io/rpi5-ros2-rrclite/): the
upstream ROS 2 port of `hector_mapping`, pinned in `hector.repos`, and `robot04_slam`, the
launch file and RViz layout for the robot's MS200 scan.

```
hector.repos            RobotX-Workshops/hector_slam_ros2 @ 87a9651 (BSD-2) -> src/hector_slam_ros2
src/robot04_slam/       launch/hector.launch.py, rviz/hector.rviz (ament_cmake)
```

## Build

```bash
sudo apt install -y python3-vcstool          # part of ros-dev-tools
git clone https://github.com/hmarthens1/robot04-hector-ws.git ~/hector_ws
cd ~/hector_ws && vcs import src < hector.repos
source /opt/ros/$ROS_DISTRO/setup.bash       # humble or jazzy
rosdep install --from-paths src --ignore-src -y
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release
source install/setup.bash
```

Keep it in `~/hector_ws`: [robot04-mapper](https://github.com/hmarthens1/robot04-mapper)
sources `~/hector_ws/install/setup.bash`.

## Run

The robot publishes `/<robot>/scan` (10 Hz) and TF `<robot>/odom -> <robot>/base_footprint ->
<robot>/laser` ([rpi5-robot-board](https://github.com/hmarthens1/rpi5-robot-board)).
Same `ROS_DOMAIN_ID` (17) and Cyclone DDS on both sides.

```bash
ros2 launch robot04_slam hector.launch.py            # robot:=robot04 rviz:=true
ros2 topic pub --once /syscommand std_msgs/String "data: reset"     # new map
```

Hector publishes `map -> <robot>/odom`, so the encoder odometry stays underneath. For a
robot without odometry, or the step-by-step lidar set-up, see
[Lab 04](https://hmarthens1.github.io/rpi5-ros2-rrclite/Lab_04/).
