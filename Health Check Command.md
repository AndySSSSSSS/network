# Cisco

Catalyst 9000系列 https://www.cisco.com/c/zh_tw/support/docs/switches/catalyst-9300-series-switches/224742-perform-catalyst-health-and.html

| show version                                        |
| --------------------------------------------------- |
| show inventory                                      |
| show interfaces status                              |
| show env all                                        |
| show logging \| tail 100<br />show logging last 100 |
| show platform resources                             |
| show processes cpu history                          |
| show memory summary<br />show processes memory      |
| show system resources                               |



# Huawei

| 设备运行的版本              | display version                                              |
| --------------------------- | ------------------------------------------------------------ |
| 检查软件包                  | display startup                                              |
| License信息                 | display license                                              |
| SN number                  | display device esn                                            |
| 检查补丁信息                | display patch-information                                    |
| 检查系统时间                | display clock                                                |
| 检查配置正确性              | display current-configuration                                |
| 运行状态                    | display device                                               |
| 风扇状态                    | display fan<br />display device fan                                                |
| 电源状态                    | display power                                                |
| 告警信息                    | display alarm all                                            |
| CPU状态                     | display cpu-usage                                            |
| 内存占用率                  | display memory-usage                                         |
| 日志信息                    | display logbuffer<br />display trapbuffer<br />display logbuffer summary<br />display logbuffer log-offset 1 size 100 |
| 温度检查                    | display temperature all                                      |
| CPCAR流量检查(Router、Core) | 先执行display再reset<br />display cpu-defend statistics all<br />reset cpu-defend statistics all |

# Nexus

https://www.cisco.com/c/zh_tw/support/docs/ios-nx-os-software/nx-os-software/217990-configure-an-automated-nexus-health-chec.html

| show  version                    |
| -------------------------------- |
| show hardware                    |
| show processes cpu history       |
| show system resources            |
| show environment                 |
| show logging last 200            |
| show inventory                   |
| show module                      |
| show featrture                   |
| show interface status            |
| show spanning-tree  blockedports |

# H3C

| dis cu              | display current-configuration |               |
| ------------------- | ----------------------------- | ------------- |
| dis ntp sta         | display ntp-service status    |               |
| dis vers            | display version               |               |
| dis device          | display device                | 检查温度      |
| dis clock           | display clock                 |               |
| dis logb            | display logbuffer             | 检测异常日志  |
| dis cpu-u           | display cpu-usage             | 检查CPU使用率 |
| dis memory          | display memory                | 检查RAM使用率 |
| dis device manuinfo |                               |               |

