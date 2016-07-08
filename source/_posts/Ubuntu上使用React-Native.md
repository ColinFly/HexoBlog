---
title: Ubuntu上使用React-Native
date: 2016-06-14 10:38:24
tags: React-Native
---


#### 1.安装NodeJs

首先要做的是安装NodeJS，这是一个目前很流行的JavaScript的实现。

启动终端，粘贴下面的命令，以从NodeSource 仓库来下载安装NodeJS：
```
sudo apt-get install -y build-essential
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo ln -s /usr/bin/nodejs /usr/bin/node
注：上面的命令是针对Ubuntu的
```

#### 2.安装 React-Native

 NodeJS包管理和分发工具,安装好NodeJs后这个应该有的,但是我没有所以:
 `sudo apt-get install npm`

`npm install -g react-native-cli `
react-native-cli是一个终端命令，它可以完成其余的设置工作。它可以通过npm安装。刚才这条命令会往你的终端安装一个叫做react-native的命令。这个安装过程你只需要进行一次。

#### 3.初始化一个项目

` react-native init AwesomeProject`
这个命令会初始化一个工程、下载React Native的所有源代码和依赖包，最后在AwesomePrjoect/iOS/AwesomeProject.xcodeproj和AwesomeProject/android/app下分别创建一个新的XCode工程和一个gradle工程。

*注意:*
由于网络原因这一步直接这样操作会慢到你爆炸,所以将npm仓库源替换为国内镜像：


`npm config set registry https://registry.npm.taobao.org`
`npm config set disturl https://npm.taobao.org/dist`


另，执行init时切记不要在前面加上sudo（否则新项目的目录所有者会变为root而不是当前用户，导致一系列权限问题，请使用chown修复）。

我是这么做的:创建一个文件夹`chomod +777 Floder`这样就没有权限问题了

#### 4运行

启动 server, 如果没有启动server , 会报错:
```
React Native: ReferenceError: Can't find variable: require (line 1 in the generated bundle)

```

`$ react-native start`
`$ react-native run-android`


#### 5.细节问题

A.如果提示要设置sdk的环境变量,就直接在在android文件夹下创建一个local.properties的文件,添加内容:sdk.dir=/opt/android-sdk-linux
也可以用命令行:`touch local.propertis && echo "sdk.dir=/opt/android-sdk-linux" >>  local.propertis`

B.跑起来的app会报红需要设置调试服务器的ip和端口,直接一条命令解决:

在`android/app/src/main`创建文件夹assets

```
curl "http://localhost:8081/index.android.bundle?platform=android" -o "android/app/src/main/assets/index.android.bundle"
```
