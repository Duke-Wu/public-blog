#### 重启pod
```
    kubectl replace --force -f xxx.yaml    # 有yaml文件的重启方式
    kubectl scale deployment esb-admin --replicas=0 -n {namespace}    # 没有yaml文件的重启方式
    kubectl scale deployment esb-admin --replicas=1 -n {namespace}
    kubectl get pod {podname} -n {namespace} -o yaml | kubectl replace --force -f -
    # 使用“-o yaml”参数导出Pod模板并重建Pod [推荐]
    # https://blog.csdn.net/weixin_44666068/article/details/102808609
```

#### 以pod形式启动了一个镜像，执行kubectl get pods 发现pod一直在重启 ，查看详细错误信息
```
Error Msg: Back-off restarting failed container
```
原因：pod命令行执行的命令执行完直接退出，然后pod就继续重启
解决办法：指定一个进程，运行在终端即可。
```
command: ["/bin/bash", "-ce", "tail -f /dev/null"]
```

#### docker镜像制作、推送（push）到 Docker Hub、迁移
https://blog.csdn.net/weixin_42867972/article/details/87988735


#### 1. 查询集群中所有的 namespaces
```
kubectl get namespaces
```


#### 2. 查询带有某个标签的node 或者 反向查询
```
kubectl get nodes --show-labels    查看现有node及label
kubectl get nodes --show-labels | grep label
kubectl get pods -l environment=production,tier=frontend
kubectl get pods -l 'environment in (production),tier in (frontend)'
kubectl get pods -l 'environment in (production, qa)'
kubectl get pods -l 'environment, environment notin (frontend)'
```


#### 3. 获取 pod 的 ip 地址
```
kubectl describe pod web-monitor-865756dbd-stbs6 -n app | grep IP | sed -E 's/IP:[[:space:]]+//'
```


#### 4. Give you a list of pods with name, status, ip, node...
```
kubectl get pods -o wide
``` 









