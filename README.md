# FiveAges W1 Description

This package contains the description files for FiveAges W1 humanoid.

## 1. Build
```bash
cd ~/ros2_ws
colcon build --packages-up-to fiveages_w1_description --symlink-install
```

## 2. Visualize the robot

* FiveAges W1 (Basic visualization)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1
  ```
      
  ![FiveAges](.images/fiveages_w1.png)

* With Grippers
  ```bash
  #Jodell RG-75 gripper with FiveAges pad
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=jodell-rg75
  ```
  ```bash
  #Jodell RG-75 gripper with LeapMotor pad
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=leapmotor
  ```
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=AG2F90-C
  ```
    ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=AG2F120S
  ```
## 3. OCS2 Demo

### 3.1 Official OCS2 Mobile Manipulator Demo

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch robot_common_launch manipulator_ocs2.launch.py robot_name:=fiveages_w1
```
[Screencast from 2025-09-03 14-26-22.webm](https://github.com/user-attachments/assets/a3f7a8b4-d47a-4e4b-bcd4-e4633a4d9ddf)


### 3.2 OCS2 Arm Controller Demo

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1 hardware:=gz world:=dart
```

* real robot
```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1 hardware:=real
```

### 4. Navigation
* Gazebo Simulation
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch diff_drive.launch.py robot:=fiveages_w1 world:=warehouse
  ```
* Real Robot
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch fiveages_w1_description navigation.launch.py
  ```
* SLAM Toolbox
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch navigation_slam.launch.py
  ```
* Cartographer
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch navigation_cartographer.launch.py
  ```