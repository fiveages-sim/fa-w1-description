# FiveAges W1 Description

This package contains the description files for FiveAges W1 humanoid.

![FiveAges](.images/fiveages_w1.png)

## 1. Build

```bash
cd ~/ros2_ws
colcon build --packages-up-to fiveages_w1_description --symlink-install
```

## 2. Visualize the robot

### 2.1 Full Robot

* FiveAges W1 (Basic visualization)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 collider:=simple
  ```


<details>
<summary><strong>More Configs</strong></summary>

  * FiveAges W1 with rg75
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=rg75
    ```

  * Switch chassis to Linkhou Q1
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=linkhou_rg75
    ```

  * Jodell RG-75 gripper with FiveAges pad
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=rg75
    ```
  * Jodell RG-75 gripper with LeapMotor pad
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=leapmotor
    ```
  * Changingtek AG2F90-C Gripper
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=AG2F90-C
    ```

</details>

### 2.2 Component

* Base
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1
  ```
* Linkhou Q1 Base
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 type:=linkhou_q1
  ```
* Upper Body
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 type:=upper_body
  ```

## 3. OCS2 Demo

### 3.1 Official OCS2 Mobile Manipulator Demo

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch robot_common_launch manipulator_ocs2.launch.py robot_name:=fiveages_w1
```

[Screencast from 2025-09-03 14-26-22.webm](https://github.com/user-attachments/assets/a3f7a8b4-d47a-4e4b-bcd4-e4633a4d9ddf)

### 3.2 OCS2 Arm Controller Demo

#### 3.2.1 Mock Component

* Empty Hand (Full Body Control)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1
  ```
* Empty Hand (Split Body Control)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1
  ```

  <details>
  <summary><strong>More Configs</strong></summary>

  * Use Jodell Hand
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=srs_rg75
    ```
  * Use Jodell Hand with Leapmotor pad
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=leapmotor
    ```
  * Use Jodell Hand and Linkhou_q1 chassis
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=linkhou_srs_rg75
    ```
  * Use Leapmotor pad and Linkhou_q1 chassis
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1 type:=linkhou_leapmotor
    ```  

</details>

#### 3.2.2 Gazebo Simulation

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=gz type:=rg75 world:=dart
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1 type:=linkhou_rg75 hardware:=gz world:=dart
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller demo.launch.py robot:=fiveages_w1 type:=leapmotor hardware:=gz world:=dart
```

#### 3.2.3 Isaac Sim Simulation

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac type:=linkhou_srs_rg75
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac type:=srs_rg75
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac type:=linkhou_ccs_rg75
```

### 3.3 Real Robot Deploy

* Build Real Robot Hardware and Controllers
  ```bash
  cd ~/ros2_ws
  colcon build --packages-up-to ocs2_arm_controller basic_joint_controller fiveages_w1_description eyou_ros2_control rokae_ros2_control --symlink-install
  ```
* Set Head Angles
  ```bash
  ros2 topic pub /head_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.5]}"  # 设置目标关节角度（弧度）
  ```
  ```bash
  ros2 topic pub /head_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.0]}"  # 设置目标关节角度（弧度）
  ```
* Set Body Angles (Only available on split control)
  ```bash
  ros2 topic pub /body_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [1.0145214890243484, -2.248923594021317, -1.2333776023323322, 0.0]}"  # 设置目标关节角度（弧度）
  ```
  ```bash
  ros2 topic pub /body_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.6810335903307411, -1.5096724218776123, -0.8279494910068607, 0.0]}"  # 设置目标关节角度（弧度）
  ```

#### 3.3.1 Launch on Laptop

* Empty Hand (Full Body Control)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=real-laptop
  ```
* Empty Hand (Split Body Control)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller split_body.launch.py  robot:=fiveages_w1 hardware:=real-laptop
  ```

* RG75 Gripper
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller split_body.launch.py  robot:=fiveages_w1 hardware:=real-laptop type:=rg75
  ```


#### 3.3.2 Launch on Robot Distrobox

* Deploy On Robot
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller full.launch.py robot:=fiveages_w1 hardware:=real
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