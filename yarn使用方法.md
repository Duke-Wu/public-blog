# yarn使用方法



## [yarn和npm命令对比](https://www.jianshu.com/p/254794d5e741)

| npm                          | yarn                 |
| ---------------------------- | -------------------- |
| npm install                  | yarn                 |
| npm install react --save     | yarn add react       |
| npm uninstall react --save   | yarn remove react    |
| npm install react --save-dev | yarn add react --dev |
| npm update --save            | yarn upgrade         |




## [yarn (npm) 切换设置镜像源](https://www.cnblogs.com/momozjm/p/10635941.html)

1、查看一下当前源

```
yarn config get registry
```

2、切换为淘宝源

```
yarn config set registry https:``//registry.npm.taobao.org
```

3、或者切换为自带的

```
yarn config set registry https:``//registry.yarnpkg.com
```



## [yarn如何全局安装命令以及和环境变量的关系](https://www.cnblogs.com/saysmy/p/9485648.html)

npm全局安装

```
npm i -g xxx
```

yarn 全局安装

```
yarn global add xxx
```

