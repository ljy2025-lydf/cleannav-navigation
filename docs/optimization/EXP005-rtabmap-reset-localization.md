
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

## EXP005 临时数据库验证（前两次无效测试）

本节使用临时数据库副本，不使用原数据库直接测试。

- 保护备份：`/home/lenovo/.ros/rtabmap_lidar.db.current_backup_20261005`
- 临时数据库：`/home/lenovo/.ros/exp005_runtime_20261006.db`、`/home/lenovo/.ros/exp005_runtime_20261007.db`
- 保护备份大小：119,787,520 字节，约 114 MB
- 保护备份 SHA256：`91e70063531bbc4d94dc743f409b4d3bfa0fe0b8eea6e3e4aa322389c74a76a2`
- 本轮不是已确认的原始约 483 MB 数据库，因此结果只能用于流程验证。

### 测试 1：默认 RTAB-Map 模式

- Gazebo 场景：`ackermann_mcity_showcase_v2`
- `/scan`：约 7.986 Hz
- `/clock`：约 9.98 Hz
- `/odom`：正常发布
- RTAB-Map：地图和定位话题正常
- 初始定位曾约为 `(-1.367, 0.000)`
- 发送目标：`map (0.0, 0.0)`
- 目标距离：约 1.37 m，满足大于 0.25 m
- 发送前 TF：先出现 `Invalid frame ID "map"`，随后坐标漂移到约 `(-3.18, 0.0)`
- 导航结果：`ABORTED`
- 导航耗时：约 18 秒
- 实际移动距离：未可靠采集
- 规划失败次数：未采集
- 重规划次数：未采集
- 急停次数：未采集
- Safety Lease 超时次数：未采集
- 时间戳过滤次数：未采集
- 有效性：无效，不计入成功率
- 失败原因：默认模式继续改变数据库和定位状态，起点不稳定。

### 测试 2：RTAB-Map localization 模式

- 数据库：`/home/lenovo/.ros/exp005_runtime_20261007.db`
- 启动参数：`localization:=true`
- 地图分辨率：`0.05 m`
- 地图尺寸：`558 x 621`
- 定位话题：有消息
- `map → base_link`：最终可查询，但启动初期出现 `Invalid frame ID "map"`
- 定位坐标：约 `(19.243, -16.178)`
- 航向：约 `-41.3°`
- 定位协方差：`9999.0`
- 导航目标：未发送
- 导航结果：无
- 实际移动距离：0 m
- 导航耗时：未采集
- 规划失败次数：未采集
- 重规划次数：未采集
- 急停次数：未采集
- Safety Lease 超时次数：未采集
- 时间戳过滤次数：未采集
- 有效性：无效，不计入成功率
- 失败原因：定位坐标与 Gazebo 当前起点不一致，且协方差为 9999，定位不可信。

### 临时结论

前两次测试均无效，尚未完成三次有效的固定起点导航测试。当前主要阻塞是：保护数据库中的定位地图与当前 Gazebo 场景或起点不一致。未修改导航参数、代价地图频率、控制器、Safety Lease、EXP004 readiness 代码或 Gazebo 地图。
