---
title: Flutter初试
date: 2023-10-19 14:57:22
tags: Flutter
categories:
- Flutter
---

## 前言
{% note success %}
[Flutter](https://flutter.cn/) 为软件开发行业带来了革新：只要一套代码库，即可构建、测试和发布适用于移动、Web、桌面和嵌入式平台的精美应用。
* **快速**
Flutter 代码可以直接编译成 `ARM` 或 `Intel` 平台的机器代码，以及 `JavaScript` 代码，确保了 Flutter 应用能够拥有原生平台的性能表现。

* **多平台支持**
**触达每个屏幕前的你的用户** 
部署到多种设备，只需要一份代码库，支持移动、网页、桌面和嵌入式设备。

* **开发体验**
**革新性的转变**
在工程中可以使用插件、自动化测试、开发者工具以及任何可以用来帮助构建高质量应用的工具。

* **稳定可依赖**
**被多数人信赖**
Flutter 由 Google 支持被并广泛使用，全球性的开发者社区广泛参与和维护，并得到众多世界知名品牌的信任。

* **Dart 编程语言**
**Flutter 由 Dart 强力驱动，助力高效构建全平台应用。**
{% endnote %}



## 搭建Flutter开发环境

Flutter有三个开发平台 macOS、Windows、Liunx、ChromeOS

### Android

#### 获取Flutter SDK
1. 去flutter官网下载其最新可用的安装包，下载地址：
https://flutter.dev/docs/development/tools/sdk/releases
{% note warning %}
注意：Flutter的渠道版本会不停变动，请以Flutter官网为准。另外，在中国大陆地区，要想正常获取安装包列表或下载安装包，可能需要翻墙，读者也可以去Flutter github项目下去下载安装包，地址：https://github.com/flutter/flutter/releases 。
{% endnote %}


2. 将安装包zip解压到你想安装Flutter SDK的路径（如：`D:\flutter`；注意:不要将flutter安装到需要一些高权限的路径如`C:\Program Files\`）。

3. 在Flutter安装目录的flutter文件下找到`flutter_console.bat`，双击运行并启动 flutter命令行，接下来，你就可以在Flutter命令行运行flutter命令了。

4. 运行 flutter doctor命令
在Flutter命令行运行如下命令来查看是否还需要安装其他依赖，如果需要，安装它们：
```
flutter doctor
```
该命令检查你的环境并在命令行窗口中显示报告。`Dart SDK`已经在打包在`Flutter SDK`里了，没有必要单独安装`Dart`。 仔细检查命令行输出以获取可能需要安装的其他软件或进一步需要执行的任务。
例如：
```
[-] Android toolchain - develop for Android devices
    • Android SDK at D:\Android\sdk
    ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
    • Try re-installing or updating your Android SDK,
      visit https://flutter.dev/setup/#android-setup for detailed instructions.
```
第一次运行flutter命令（如`flutter doctor`）时，它会下载它自己的依赖项并自行编译。以后再运行就会快得多。缺失的依赖需要安装一下，安装完成后再运行`flutter doctor`命令来验证是否安装成功。

#### VSCode 配置和使用Flutter
1. 安装Flutter和Dart插件
* `Flutter`插件： 支持Flutter开发工作流 (运行、调试、热重载等)。
* `Dart`插件： 提供代码分析 (输入代码时进行验证、代码补全等)。
2. 新建项目




### iOS

待填充
