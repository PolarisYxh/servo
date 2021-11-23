## Moveit kinova Servo use gazebo
#### Quick Start Guide


Build and subsequently source the catkin workspace. Startup the robot and MoveIt:  需要gazebo

    roslaunch kinova_gazebo robot_launch.launch use_trajectory_controller:=false

    roslaunch j2n6s300_moveit_config j2n6s300_gazebo_demo.launch

注释：kinova_control.launch     //   里面将/j2n6s300/joint_states改为/joint_states
j2n6s300_gazebo_demo.launch

Launch the servo node. This example uses commands from a SpaceNavigator joystick-like device:

    roslaunch j2n6s300_moveit_config unity_connect_close.launch  //闭环控制 不需要gazebo

    roslaunch kinova_moveit_servo spacenav_cpp_kinova.launch
    用
    rosparam set /servo_server/parameter_ns optional_parameter_namespace
    cd config
    rosparam load kinova_simulated_config.yaml servo_server/optional_parameter_namespace
    rosrun spacenav_node spacenav_node
    rosrun my_moveit_servo spacenav_to_twist
    rosrun my_moveit_servo servo_server
    四条代替
If you dont have a SpaceNavigator, send commands like this:

```sh
rostopic pub -r 100 /servo_server/delta_twist_cmds geometry_msgs/TwistStamped "header: auto
twist:
  linear:
    x: 0.01
    y: 0.0
    z: -0.0
  angular:
    x: 0.0
    y: 0.0
    z: 0.0"
```

If you see a warning about "close to singularity", try changing the direction of motion.

#### Running Tests

Run tests from the moveit\_servo folder:

    catkin run_tests --no-deps --this

## Moveit kinova Servo use unity
1、roslaunch j2n6s300_moveit_config j2n6s300_gazebo_demo_close_unity.launch

## Moveit ur Servo

#### Quick Start Guide for UR5 example

Clone the `universal_robot` repo into your catkin workspace:

    git clone https://github.com/ros-industrial/universal_robot.git

Run `rosdep install` from the `src` folder to install dependencies.

    rosdep install --from-paths . --ignore-src -y

Build and subsequently source the catkin workspace. Startup the robot and MoveIt:

    roslaunch ur_gazebo ur5.launch

    roslaunch ur5_moveit_config ur5_moveit_planning_execution.launch sim:=true

    roslaunch ur5_moveit_config moveit_rviz.launch config:=true

In RViz, "plan and execute" a motion to a non-singular position (not all zero joint angles) that is not close to a joint limit.

Switch to a compatible type of `ros-control` controller. It should be a `JointGroupVelocityController` or a `JointGroupPositionController`, not a trajectory controller like MoveIt usually requires.

```sh
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'arm_controller'
strictness: 2"
```

Launch the servo node. This example uses commands from a SpaceNavigator joystick-like device:

    roslaunch moveit_servo spacenav_cpp.launch
    用
    rosparam set /servo_server/parameter_ns optional_parameter_namespace
    rosparam load ur_simulated_config.yaml servo_server/optional_parameter_namespace
    rosrun spacenav_node spacenav_node
    rosrun my_moveit_servo spacenav_to_twist
    rosrun my_moveit_servo servo_server
    四条代替
If you dont have a SpaceNavigator, send commands like this:

```sh
rostopic pub -r 100 /servo_server/delta_twist_cmds geometry_msgs/TwistStamped "header: auto
twist:
  linear:
    x: 0.0
    y: 0.01
    z: -0.01
  angular:
    x: 0.0
    y: 0.0
    z: 0.0"
```

If you see a warning about "close to singularity", try changing the direction of motion.

#### Running Tests

Run tests from the moveit\_servo folder:

    catkin run_tests --no-deps --this