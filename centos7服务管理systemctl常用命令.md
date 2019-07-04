## centos7服务管理systemctl常用命令

有时候配置服务需要用到，特此留存：

1. systemctl 服务主体存贮目录： 
   /usr/lib/systemd/system/

2. systemctl 服务开机启动链接存贮目录： 
   /etc/systemd/system/basic.target.wants/

3. 列出所有开机自启的服务 
   systemctl  list-unit-files  |  grep enabled

4. 列出防火墙服务的自启状态 
   systemctl  list-unit-files | grep  firewalld

5. 列出所有服务的层级和依赖关系，可以指定某个服务 
   systemctl list-dependencies [服务名称]