---
title: ReactNative初试
# index_img: /img/acrodesign/reactnative.png
date: 2023-09-15 13:38:28
tags: ReactNative
categories:
- ReactNative
---

## 前言
{% note success %}
[React Native](https://reactnative.dev/) 是一个使用 JavaScript 和 React 开发本地Android和iOS应用的框架，他有以下优点: 
* **简单易开发**
React Native 使你可以创建真正原生的应用，用户体验绝不拉胯。它提供了一些平台无关的抽象核心组件，像是`View`, `Text` 以及 `Image`等，可直接映射渲染为 对应平台的原生UI组件。

* **无缝跨平台**
通过 React 的声明式组件机制和 JavaScript 代码，现有的原生代码和api可以完美地封装嵌合到 React 组件中。这样既为更多新的开发团队赋予原生应用的开发能力，也能极大地提升现有原生团队的开发效率。

* **秒速刷新**
保存即刷新  借助 JavaScript 的动态特性， React Native 能够让你光速迭代。
{% endnote %}



## 搭建开发环境

React Native有三个开发平台 macOS、Windows Liunx

### Android

#### 首先你需要在你的电脑上先安装 [Node](https://nodejs.org/en)、[JDK](https://www.oracle.com/java/technologies/downloads/#java11)和[Android Studio](https://developer.android.com/studio)这些项目依赖后才能进行React Native项目的创建
{% note warning %}
注意事项：
* Node 的版本应大于等于 16，安装完 Node 后建议设置 npm 镜像（淘宝源）以加速后面的过程
* 不要使用 cnpm！cnpm 安装的模块路径比较奇怪，packager 不能正常识别！
* React Native 需要 `Java Development Kit [JDK] 11`,你可以在命令行中输入 `javac -version`来查看你当前安装的 JDK 版本。如果版本不合要求，则可以去[Temurin](https://adoptium.net/zh-CN/temurin/releases/?variant=openjdk11&jvmVariant=hotspot)或[Oracle JDK](https://www.oracle.com/java/technologies/downloads/#java11)上下载
{% endnote %}

#### 配置环境变量
React Native 需要通过环境变量来了解你的 Android SDK 装在什么路径，从而正常进行编译。
* 右键`我的电脑`>`属性`>`高级系统设置`>`高级`>`环境变量`>`新建`,创建一个名为ANDROID_HOME的环境变量（系统或用户变量均可），指向你的 Android SDK 所在的目录（具体的路径可能和下图不一致，请自行确认）：

![](../img/blogimg/9-15/01.png)

* 使用Android Studio 时需要安装模拟器，但是坑人的是他的模拟器只能安装在`C盘`里面，安装完成后大概能占个5G的存储空间,如果你不想当`C盘战士`的话就得将他移到别的空闲的磁盘中,这时我们可以定义一个系统变量来改变它的默认路径指向

![](../img/blogimg/9-15/02.png)

>我这里是将它的默认路径改在了`D:\android_AVD`文件夹中,你如果想装在其他的盘中，把变量值改成你文件夹的对应路径即可

#### 创建新项目

1. 准备工作做完后可以开始创建React Native项目了在你项目文件夹中打开命令提示框 输入 
`npx react-native@latest init 项目名称`
{% note warning %}
注意事项：
* 如果你之前全局安装过旧的`react-native-cli`命令行工具，请使用`npm uninstall -g react-native-cli`卸载掉它以避免一些冲突：
```
npm uninstall -g react-native-cli @react-native-community/cli
```

* 文件名中不能使用`中文`、`空格`等特殊符号。也不能使用常见的关键字作为项目名（如 `class`, `native`, `new`, `package` 等等）。也不能使用与核心模块同名的项目名（如 `react`, `react-native` 等）。

{% endnote %}

2.安装成功后的界面应该是这样的

![](../img/blogimg/9-15/03.png)

3.接下来就可以启动项目了
```BASH
# 进入你的项目
cd AwesomeProject

# 运行
npx react-native run-android

# 运行完后会打开一个新的命令提示框并显示4个选项

r - reload the app
d - open developer menu
i - run on iOS
a - run on Android 

Android 输入 a 即可

```
![](../img/blogimg/9-15/04.png)
![](../img/blogimg/9-15/05.png)
![](../img/blogimg/9-15/06.png)

{% note warning %}
若启动不成功可能是端口被占用，只需要修改端口号即可

```
npx react-native run-android --port=1234
```
构建命令
```
npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
```
{% endnote %}

#### 打包
**1. 生成签名秘钥：**
你可以用keytool命令生成一个私有密钥。在Windows上keytool命令放在JDK的bin目录中（比如`..\jdkx.x.x_x\bin`），
进入你放`JDK`的目录，点文件地址栏输入`cmd` 接着输入以下命令 按提示操作即可

```
    $ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```
* 这条命令会要求你输入密钥库（keystore）和对应密钥的密码，然后设置一些发行相关的信息。最后它会生成一个叫做`my-release-key.keystore`的密钥库文件。
* 在运行上面这条语句之后，密钥库里应该已经生成了一个单独的密钥，有效期为10000天。--alias参数后面的别名是你将来为应用签名时所需要用到的，所以记得记录这个别名。

**2. 设置gradle变量：**
把`my-release-key.keystore`文件放到你工程中的`android/app`文件夹下。
编辑`android/gradle.properties`（没有这个文件你就创建一个），添加如下的代码（注意把其中的****替换为相应密码）
```
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=*****
MYAPP_RELEASE_KEY_PASSWORD=*****
```

**3. 添加签名到应用的gradle配置中：**
编辑你项目目录下的`android/app/build.gradle`，添加如下的签名配置：
```
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            storeFile file("my-release-key.keystore")
            storePassword ******
            keyAlias "my-key-alias"
            keyPassword ******
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```
**4.打包：**
打包的话需要注意，在打包之前要先将`android\app\src\main\res`文件下所有`drawable-*`的文件全部删掉 否则会报错。
准备好之后，进入`android`文件夹，输入`cmd` 进入命令行，运行打包`gradlew assembleRelease` 即可。
为了加快效率 ，我这边是我们后端给我创建了一个bat的命令行进行的，只需要点击一下即可开始打包，如有需要自取，文件放置在`\android`目录下即可。
```
//  *** 这里写的是你放置项目文件的绝对路径 从盘开始 例如C:\Program Files

rmdir /s /q "***\android\app\src\main\res\drawable-hdpi"
rmdir /s /q "***\android\app\src\main\res\drawable-mdpi"
rmdir /s /q "***\android\app\src\main\res\drawable-xhdpi"
rmdir /s /q "***\android\app\src\main\res\drawable-xxhdpi"
rmdir /s /q "***\android\app\src\main\res\drawable-xxxhdpi"

start gradlew.bat assembleRelease
```
生成的 APK 文件位于`android/app/build/outputs/apk/release/app-release.apk`，它已经可以用来发布了。




### iOS

待填充



