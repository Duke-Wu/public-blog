wget是linux下常用的命令行下载工具，是Linux用户是必不可少的工具，尤其对于网络管理员，经常要下载一些软件。而tget是一个简单的命令行BT下载工具，可以用于BT种子和磁力链接的下载。



tget使用`Nodejs`开发，基于 `torrent-stream` ，代码量很少。

tget源代码： `https://github.com/jeffjose/tget` 类似的项目： `https://github.com/mafintosh/peerflix` 
安装tget：

```bash
npm install -g t-get
```

torrent种子文件的下载：

```bash
$ wget http://releases.ubuntu.com/16.04/ubuntu-16.04.1-desktop-amd64.iso.torrent $ tget ubuntu-16.04.1-desktop-amd64.iso.torrent 
```

tget-用于BT种子/磁力链接下载的wget 
磁力链接的下载：

```bash
tget 'magnet:?xt=urn:btih:0403fb4728bd788fbcb67e87d6feb241ef38c75a'
```

