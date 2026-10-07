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
