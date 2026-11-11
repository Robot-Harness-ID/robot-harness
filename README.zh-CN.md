# Robot Harness

[English](README.md) | **简体中文**

Robot Harness 是一个开源的机器人辨识、执行与评测层，位于通用 Agent 与真实机器人之间。

项目目标不只是求解标定参数，而是测量：在限定的标定、交互和人工干预预算内，一个冻结的 Agent 能多可靠地操作真实机器人。

`ID` 同时代表 **Identification（辨识）** 和 **Identity（身份）**：每台机器人都应具有版本化的描述，包括几何、传感、控制语义、实测能力和标定健康状态。

> **当前状态：项目启动阶段。** 本仓库目前包含数据规范和首个基线协议，尚未包含运动运行时。NERO + D435i 参考配置尚未经过本项目的真机验证。

## 基本原则

- 在评测 Agent 之前，先建立不依赖 Agent 的可重复基线。
- 区分机器人本体、感知、标定、执行和规划故障。
- 标定位姿与验证位姿必须分离。
- 不仅凭编码器正运动学或标定残差推断绝对精度。
- 记录失败试验、软件版本、标定版本和人工干预。
- 将自我改进视为经过验证和回滚机制约束的外部产物更新，而不是不受限制地自我修改。

## 架构

```text
API Agent / VLA
      |
      v
Robot Harness
  |- 能力画像与健康状态
  |- 本体适配器与单一写入者控制语义
  |- 局部执行技能与恢复
  |- 参数辨识与标定
  |- 执行记录与独立评测
      |
      v
真实机器人

独立硬件安全始终位于 Agent 和学习闭环之外。
```

## 首个参考配置

- AgileX NERO 七轴机械臂
- Intel RealSense D435i，固定安装，采用 eye-to-hand 配置
- 位置控制；初始基线不使用 MIT 控制
- 建议使用 Ubuntu 22.04 和 ROS 2 Humble 作为参考环境
- 使用 AgileX 和 RealSense 官方驱动，并在设备接入时记录准确版本

基线定义见 [robots/nero_d435i/baseline.json](robots/nero_d435i/baseline.json) 和 [robots/nero_d435i/README.md](robots/nero_d435i/README.md)。

## 里程碑

1. **B0——只读健康检查：** 检查关节和相机观测、坐标系、时间戳、诊断信息及版本记录，不执行运动。
2. **B1——机器人基线：** 在不使用相机目标的情况下，执行重复关节运动和已知真值的笛卡尔目标。
3. **B2——参数辨识：** 完成 TCP 与手眼标定，使用留出集验证并生成版本化标定产物。
4. **B3——感知基线：** 对比真值目标、单次视觉目标和有界视觉修正。
5. **B4——冻结 Agent：** 暴露少量技能接口，评测可靠性、人工干预、重试和适配成本。

发布 `v0.1.0` 的前提是 B0–B3 能从同一个代码版本复现，包括全部失败试验，并明确说明哪些测量是独立的。

## 仓库结构

```text
specs/                 版本化数据契约
robots/nero_d435i/     首个参考配置与基线协议
harness_core/          规划中的运行时健康检查与执行原语
harness_id/            规划中的标定与参数辨识工具
harness_eval/          规划中的试验执行与报告工具
```

尚未填充的目录均为规划内容，不代表已经实现的功能。

## 官方硬件资源

- [AgileX ROS 2 驱动](https://github.com/agilexrobotics/agx_arm_ros)
- [AgileX Python SDK](https://github.com/agilexrobotics/pyAgxArm)
- [NERO 首次 CAN 使用指南](https://github.com/agilexrobotics/pyAgxArm/blob/master/docs/nero/first_time_user_guide_can.md)
- [RealSense ROS 驱动](https://github.com/realsenseai/realsense-ros)

## 校验仓库产物

当前检查仅要求安装 Python 3：

```bash
python3 -m json.tool specs/robot-profile.schema.json >/dev/null
python3 -m json.tool specs/trial-result.schema.json >/dev/null
python3 -m json.tool robots/nero_d435i/baseline.json >/dev/null
```

这些检查只验证 JSON 语法，不验证硬件行为，也不进行 JSON Schema 合规校验。

## 安全

本仓库中的任何软件都不能替代独立急停、工作空间风险评估或机器人厂商的安全规程。首次真机操作必须只读。后续运动实验要求低速、受限工作空间、单一命令写入者，并由负责人员留在机器人运动路径之外。

## 参与贡献

请参阅 [CONTRIBUTING.md](CONTRIBUTING.md)。硬件支持声明必须区分已实现、已回放测试和已经真机验证的功能。

## 许可证

Apache-2.0，详见 [LICENSE](LICENSE)。
