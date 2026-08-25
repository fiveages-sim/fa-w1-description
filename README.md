# FiveAges W1 Description

轮式人形 W1 的 URDF / ros2_control 描述包。模型在 **`xacro/`**。底盘与躯干在本包；双臂复用 [`ar5_ccs_description`](../../../../manipulator/Rokae/ar5_ccs_description/README.md) / [`ar5_srs_description`](../../../../manipulator/Rokae/ar5_srs_description/README.md)，末端走 CCS 包的 `eef_type_resolver.xacro` / `eefs.xacro`。

```bash
cd ~/ros2_ws && colcon build --packages-up-to fiveages_w1_description --symlink-install
source install/setup.bash
```

![FiveAges W1](.images/fiveages_w1.png)

## 1. 可视化与 Launch

| 场景 | 入口 | 示例 |
|------|------|------|
| 全身 RViz | `humanoid.launch.py` | `… robot:=fiveages_w1 collider:=simple` |
| 换底盘 / 换臂 | 同上 + `chassis:=` / `arms:=` | `chassis:=linkhou_q1`、`arms:=ar5_ccs` |
| 带末端 | 同上 + `type:=<key>` | `… type:=rg75`（键名见 [ar5_ccs §2](../../../../manipulator/Rokae/ar5_ccs_description/README.md#2-末端执行器)） |
| 左右不同末端 | `left_type` / `right_type` | `left_type:=rg75 right_type:=ag2f90_c` |
| 部件 | `component.launch.py` | 默认底盘；`type:=arms` / `upper_body`；`chassis:=linkhou_q1` |
| 全身 WBC（mock） | `ocs2_arm_controller full_body.launch.py` | 默认 `hardware:=mock_components` |
| 分体控制（mock） | `split_body.launch.py` | 腰 / 头 / 臂独立控制器 |
| Isaac / Gazebo | 上述 launch + `hardware:=isaac` / `gz` | 仿真桥接 |
| 导航 | `robot_common_launch` | 见 [§6](#6-导航) |

```bash
ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 collider:=simple
ros2 launch robot_common_launch humanoid.launch.py robot:=fiveages_w1 chassis:=linkhou_q1 arms:=ar5_ccs type:=rg75
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1
ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 arms:=ar5_ccs
```
  
## 2. 末端执行器

`type` / `left_type` / `right_type` 与 AR5 CCS **同一套键**（解析：`ar5_ccs_description/xacro/eef_type_resolver.xacro`）。

真机驱动方式、支持矩阵等详见 **[ar5_ccs README §2](../../../../manipulator/Rokae/ar5_ccs_description/README.md#2-末端执行器)**，此处不重复。

W1 常用：`rg75`、`leapmotor`（RG75 + LeapMotor pad）、`ag2f90_c`。

## 3. 平台与模型

| 槽 | Launch | 取值 | 默认 |
|----|--------|------|------|
| 底盘 | `chassis:=` | `w1` / `linkhou_q1` | `w1` |
| 双臂 | `arms:=` | `ar5_ccs`（CCS v1） / `ar5_srs` | `ar5_srs` |

W1 没有独立的 `variant:=` 槽，也不支持 `ar5_ccs_v2`。独立 AR5 包内选 CCS 代际仍用 `variant:=`，见 [ar5_ccs README](../../../../manipulator/Rokae/ar5_ccs_description/README.md)。

| 层级 | xacro | 说明 |
|------|-------|------|
| 底盘 | `xacro/chassis/chassis.xacro`、`linkhou_q1.xacro` | 轮关节默认 `fixed`（`chassis_joints_movable:=false`）；不进 ros2_control |
| 躯干 / 头 | `xacro/body.xacro` | 接臂点 `arm_base` |
| 双臂 | `ar5_ccs` `arm_ccs.xacro` 或 `ar5_srs` `arm_srs.xacro` | 由 `arms:=` 选择 |
| 末端 | `ar5_ccs` `eefs.xacro` | 与独立 AR5 一致 |

规划 URDF 默认冻结底盘轮关节与末端主动关节（`chassis_joints_movable:=false`、`eef_fixed_joints` 由 launch 设置）。整机若要转轮，显式 `chassis_joints_movable:=true`。部件预览 `component.launch.py` 默认轮关节可动。

## 4. ros2_control 架构

真机拆为左右臂 + 腰头；仿真合并为一个。底盘轮关节不进 ros2_control。

| 模式 | System | 硬件插件 |
|------|--------|----------|
| 真机 | `ar5_left_system` / `ar5_right_system` | `rokae_ros2_control` |
| 真机 | `eyou_body` | `eyou_ros2_control`（`body_joint1~4`、`head_joint1~2`） |
| 仿真 | `fiveages_w1_system` | mock / gz / isaac |

手臂侧接口宏来自 `ar5_ccs_description`：`dual_arm_ee_block` / `side_arm_ee_block`。本包 `xacro/ros2_control/robot.xacro` 为薄封装。

控制器配置：`config/ros2_control/common.yaml`（`ros2_controllers.yaml` 为 overlay）。

| 模式 | 控制器 | 任务文件 | 规划 URDF |
|------|--------|----------|-----------|
| 全身 WBC（`full_body`） | `ocs2_wbc_controller`（`Ocs2WbcController`） | `config/ocs2/task.info`（差速 `manipulatorModelType=1`；关节顺序 `[腰×4 \| 左臂×7 \| 右臂×7 \| 头×2]`） | `robot.xacro` `topology:=full`；RViz `config/rviz/fullbody.rviz`（Fixed Frame=`world`） |
| 分体（`split_body`） | `ocs2_arm_controller` + `body_joint_controller` / `head_joint_controller` | `config/ocs2/dual_arm.info` | `robot.xacro` + `topology:=dual`（根 `arm_base`，CCS/SRS 法兰都是 ±0.12）；RViz `splitbody.rviz`（Fixed Frame=`base_footprint`） |

全身 WBC 把腰/头/双臂放进同一个控制器；底盘是虚拟 SE(2)（轮关节不进 ros2_control）。分体时腰/头仍走 `basic_joint_controller`。

## 5. 真机部署

```bash
colcon build --packages-up-to ocs2_wbc_controller ocs2_arm_controller basic_joint_controller fiveages_w1_description eyou_ros2_control rokae_ros2_control --symlink-install
source install/setup.bash

ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=real
ros2 launch ocs2_arm_controller split_body.launch.py robot:=fiveages_w1 hardware:=real type:=rg75

# 笔记本连真机
ros2 launch ocs2_arm_controller full_body.launch.py robot:=fiveages_w1 hardware:=real-laptop
```

`robot.local.yaml` 片段：

```yaml
platform:
  chassis: linkhou_q1
  arms: ar5_srs
defaults:
  end_effectors:
    type: rg75
  tcp_offset:
    xyz: "0 0 0"
    rpy: "0 0 0"
```

Launch 参数覆盖 profile，优先级 CLI > profile > xacro 默认。

### 分体控制常用话题（`split_body`）

```bash
# 头
ros2 topic pub /head_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray "{data: [0.0, 0.5]}"
# 腰（仅 split_body）
ros2 topic pub /body_joint_controller/target_joint_position std_msgs/msg/Float64MultiArray \
  "{data: [1.014, -2.249, -1.233, 0.0]}"
# 双臂（14 维）
ros2 topic pub /ocs2_arm_controller/target_joint_position std_msgs/msg/Float64MultiArray \
  "{data: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]}"
```

## 6. 导航

```bash
ros2 launch robot_common_launch navigation_isaac_gt.launch.py robot:=fiveages_w1
```

Nav2 / EKF 配置在 `config/nav2/`。
