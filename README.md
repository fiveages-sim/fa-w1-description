# FiveAges W1 Description

This package contains the description files for FiveAges W1 humanoid.

![FiveAges](.images/fiveages_w1.png)

FiveAges W1 用独立槽位选择底盘和双臂，末端执行器走 `type:=`，不再把三者拼进一个 `type` 字符串。

## 1. Build

```bash
cd ~/ros2_ws
colcon build --packages-up-to fiveages_w1_description --symlink-install
```

Platform slots:

| 槽 | Launch | 取值 | 默认 |
|----|--------|------|------|
| 底盘 | `chassis:=` | `w1` / `linkhou_q1` | `w1` |
| 双臂 | `arms:=` | `ar5_ccs`（CCS v1） / `ar5_srs` | `ar5_srs` |
| 底盘关节 | `chassis_joints_movable:=` | `true` / `false` | 整机 `false`（组件预览默认 `true`） |

W1 不支持 `ar5_ccs_v2`。CCS 代数键与独立 AR5 包一致，见 [ar5_ccs README](../../../../manipulator/Rokae/ar5_ccs_description/README.md)。

```bash
ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 chassis:=w1 arms:=ar5_srs
ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 arms:=ar5_ccs
ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 chassis:=linkhou_q1 arms:=ar5_srs
```

同一选择也可写在 `config/robot.local.yaml`：

```yaml
platform:
  chassis: linkhou_q1
  arms: ar5_ccs
```

W1 没有 `variant` 槽：选臂用 `arms:=`，选底盘用 `chassis:=`。`type:=` 只选末端，例如 `rg75`、`leapmotor`、`ag2f90_c`。独立 AR5 包内选 CCS 代际仍用 `variant:=`。

整机启动时轮关节默认 `fixed`（`w1` 与 `linkhou_q1` 相同，与 W2 一致），不会进 ros2_control，也不会出现在 `/joint_states`。需要轮子可动时显式打开：

```bash
ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 chassis:=linkhou_q1 chassis_joints_movable:=true
ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 chassis:=linkhou_q1
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

  * Switch chassis to Linkhou Q1
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 chassis:=linkhou_q1
    ```
  * AR5 CCS v1 dual arms
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 arms:=ar5_ccs
    ```
  * Jodell RG-75 gripper
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=rg75
    ```
  * Jodell RG-75 with LeapMotor pad
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=leapmotor
    ```
  * ChangingTek AG2F90-C gripper
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 type:=ag2f90_c
    ```

</details>

### 2.2 Component

* Base (default `chassis:=w1`)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 chassis:=linkhou_q1
  ```
* Dual arms (default `arms:=ar5_srs`)
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 type:=arms
  ros2 launch robot_common_launch component.launch.py robot:=fiveages_w1 type:=arms arms:=ar5_ccs
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

  * Use Jodell RG75
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=rg75
    ```
  * Use LeapMotor pad
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=leapmotor
    ```
  * Linkhou Q1 chassis + AR5 SRS
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 chassis:=linkhou_q1 arms:=ar5_srs type:=rg75
    ```
  * Native chassis + AR5 CCS v1
    ```bash
    source ~/ros2_ws/install/setup.bash
    ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 arms:=ar5_ccs
    ```

</details>

#### 3.2.2 Gazebo Simulation

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=gz type:=rg75 world:=dart
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 chassis:=linkhou_q1 type:=rg75 hardware:=gz world:=dart
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 type:=leapmotor hardware:=gz world:=dart
```

#### 3.2.3 Isaac Sim Simulation

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac chassis:=linkhou_q1 arms:=ar5_srs type:=rg75
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac type:=rg75
```

```bash
source ~/ros2_ws/install/setup.bash
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=isaac arms:=ar5_ccs type:=rg75
```

### 3.3 Real Robot Deploy

* Build Real Robot Hardware and Controllers
  ```bash
  cd ~/ros2_ws
  colcon build --packages-up-to ocs2_arm_controller basic_joint_controller fiveages_w1_description eyou_ros2_control rokae_ros2_control --symlink-install
  ```
* Set Head Angles
  ```bash
  ros2 topic pub /head_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.5]}"
  ```
  ```bash
  ros2 topic pub /head_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.0]}"
  ```
* Set Body Angles (Only available on split control)
  ```bash
  ros2 topic pub /body_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [1.0145214890243484, -2.248923594021317, -1.2333776023323322, 0.0]}"
  ```
  ```bash
  ros2 topic pub /body_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.6810335903307411, -1.5096724218776123, -0.8279494910068607, 0.0]}"
  ```
* Set Arm Angles
  ```bash
  ros2 topic pub /ocs2_arm_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]}"
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
  ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 hardware:=real-laptop
  ```
* RG75 Gripper
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 hardware:=real-laptop type:=rg75
  ```

#### 3.3.2 Launch on Robot Distrobox

* Deploy On Robot
  ```bash
  source ~/ros2_ws/install/setup.bash
  ros2 launch ocs2_arm_controller full.launch.py robot:=fiveages_w1 hardware:=real
  ```

### 4. Navigation

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
