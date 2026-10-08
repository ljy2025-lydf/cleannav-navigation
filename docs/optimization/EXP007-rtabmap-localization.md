# EXP007 RTAB-Map LiDAR 重定位失败分析

## 基线

- 分支：ljy
- 数据库：`/home/lenovo/.ros/exp006_mcity_matching.db`
- 数据库大小：约 7.2 MB
- `Reg/Strategy=1`
- `Reg/Force3DoF=true`
- `Icp/MaxCorrespondenceDistance=0.1`
- `Icp/CorrespondenceRatio=0.1`
- `Icp/Iterations=30`
- `Icp/VoxelSize=0.05`
- `RGBD/OptimizeMaxError=3.0`
- `Mem/IncrementalMemory=false`
- `Mem/InitWMWithAllNodes=true`

## 观测结果

- localization_pose 有消息；
- 定位坐标约为 `(1.88, 0.03)`；
- 定位协方差持续为 `9999.0`；
- `map → base_link` 可以查询；
- 启动阶段出现 `Invalid frame ID "map"`；
- 当前没有证据证明单一 ICP 参数是根因。

## 当前结论

EXP007 暂停参数修改。当前更可能需要先增加数据库地图覆盖范围和结构特征，再进行 ICP 参数对照。此次未修改 RTAB-Map 参数、导航参数或代码。

## 导航 readiness 验证（无效测试）

本次使用 `/home/lenovo/.ros/exp007_mcity_mapping.db`，场景和数据库未更换。

- `/clock`：约 9.98 Hz
- `/odom`：约 99.7 Hz
- `/scan`：约 7.98 Hz
- TF 拓扑：存在 `map -> odom -> base_footprint -> base_link`
- RTAB-Map 定位消息：存在，协方差为有限值
- readiness 状态：失败
- 主要日志：
  - `Waiting: TF map <- odom at odom stamp`
  - `Waiting: stability window`
  - `READINESS TIMEOUT: stability window`
- 导航目标：未发送
- 导航结果：未采集
- 导航耗时：未采集
- 实际移动距离：未采集
- 重规划次数：未采集
- 急停次数：未采集
- Safety Lease 超时次数：未采集
- 有效性：无效，不计入导航成功率

结论：当前主要阻塞是 navigation readiness 的 TF 时间稳定性检查，尚不能进行有效的 ICP 参数对照。本次未修改 ICP、导航、控制器、Safety Lease 或代价地图参数。
