
# EXP005 RTAB-Map 重置与固定起点（追加记录）

## 基线
分支：ljy；工作区开始时干净。RTAB-Map 数据库：/home/lenovo/.ros/rtabmap_lidar.db，大小约 483 MB。导航参数保持 EXP004 状态，local_costmap update_frequency=10.0 Hz、publish_frequency=2.0 Hz。

reset 前观察到 map 到 base_link 可查询，示例坐标约 (2.866,-1.795)，随后一次观察约 (3.435,-2.294)，说明当前数据库增量定位已经存在漂移。/rtabmap/map 约 1 Hz，/odom 约 100 Hz，/scan 约 7.99 Hz；/rtabmap/localization_pose 未稳定发布。

## 方案选择
选择“每次测试重新启动 RTAB-Map 并加载同一数据库”作为唯一恢复方案。官方 /rtabmap/rtabmap/reset 服务类型为 std_srvs/srv/Empty，但实测调用后 /rtabmap/map 和 map 到 base_link 输出暂时消失，因此不采用该服务作为测试恢复机制。没有修改项目代码、导航参数、Gazebo 场景、数据库或 EXP004 readiness 逻辑。

## reset 试验
调用官方 reset 服务后，服务返回成功，但随后地图话题无法稳定确定类型，map 到 base_link 在等待窗口内不可用；该次标记无效。由于 RTAB-Map 进程随后未恢复，本轮没有伪造三次成功率、起点误差或导航距离数据。

| 测试 | reset 后 TF | 地图/定位 | 导航结果 | 有效性 |
|---|---|---|---|---|
| 官方 reset 1 | 不可用 | /rtabmap/map 暂停 | 未发送目标 | 无效 |

规划次数、重规划次数、急停次数、Safety Lease 超时次数和耗时：未采集。

## 结论
EXP005 已确认官方 reset 服务不能单独恢复可重复 RTAB-Map 定位；需要完整停止并重新启动 RTAB-Map，加载同一数据库后再测试。当前运行环境在 reset 后未恢复 RTAB-Map，因此三次固定起点测试尚未完成。本节只追加记录，未覆盖 EXP001 到 EXP004。

## EXP005 前置条件检查（追加记录）

检查日期：2026-10-05。

- 当前分支：ljy。
- 工作区：无未提交修改。
- 当前 HEAD：059a253b920434743a824df3d92bfcf45aea34cc。
- upstream/ljy：b82869ff3c516b8e6dfdc7b9a472de4307adb7cf。
- 当前数据库：/home/lenovo/.ros/rtabmap_lidar.db。
- 当前数据库大小：约 38.7 MB。
- 在 /home/lenovo 和 /mnt/c/Users/lenovo 中未找到约 483 MB 的原始数据库备份。
- /mnt/d 全盘扫描因耗时过长停止，未得到可确认的备份候选。

按照实验要求，本轮没有复制、覆盖、删除或启动会写入数据库的 RTAB-Map 进程，也没有修改代码或导航参数。由于找不到明确的原始数据库快照，EXP005 三次固定起点测试暂停，等待提供备份位置或重新生成可确认的只读数据库快照。
