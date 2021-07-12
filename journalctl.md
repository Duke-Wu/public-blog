
1. journalctl
https://www.chenweiliang.com/cwl-1141.html#journalctl-2

journalctl -f -u some.service    实时查看日志
journalctl -n 20    显示最新20行
journalctl --disk-usage    日志占用磁盘容量
journalctl --vacuum-time=2d    仅保留2天以前的日志
journalctl --vacuum-size=500M    仅保留500M的日志
