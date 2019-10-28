---
title: 选择用于通过 Visual Studio 和 Azure 服务生成客户端应用程序的前端开发平台
description: 了解支持的本机和跨平台语言，以生成客户端应用程序。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795492"
---
# <a name="choose-mobile-development-framework"></a>选择移动开发框架
开发人员可以使用客户端技术自行构建移动应用程序，使用特定的框架和模式实现跨平台方法。 开发人员可以通过使用 Xamarin、.NET C#和的跨平台应用程序、混合（使用 Cordova）及其变体构建本机（本机单一平台，使用的语言如客观-C 和 Java）造成.

## <a name="native-platforms"></a>本机平台
构建本机应用程序需要特定于平台的编程语言、Sdk、开发环境和 OS 供应商提供的其他工具。

### <a name="ios"></a>iOS
由 Apple 创建和开发，在 iOS 上构建的应用在 Apple 设备上运行，即 iPhone 和 iPad。

- **编程语言**-目标-C，Swift
- **IDE** -Xcode
- **SDK** -iOS SDK

### <a name="android"></a>Android
基于 Google 和世界上最常用的操作系统，在 Android 上构建的应用程序可以在一系列智能手机和平板电脑上运行。

- **编程语言**-Java、Kotlin 
- **IDE** -Android Studio 和 Android 开发人员工具 
- **SDK** -Android SDK

### <a name="windows"></a>Windows
- **编程语言**-C#
- **IDE** -Visual Studio，Visual Studio Code
- **SDK** -Windows SDK

#### <a name="pros"></a>优点
- 良好的用户体验
- 具有高性能和可与本机库交互的能力的响应式应用程序
- 高度安全的应用程序

#### <a name="cons"></a>缺点
- 应用程序只能在一个平台上运行
- 构建应用程序的更多开发人员资源和资源
- 更低的代码重用

## <a name="cross-platforms-and-hybrid-applications"></a>跨平台和混合应用程序
跨平台应用程序提供了一次编写本机移动应用程序、共享代码以及在 iOS、Android 和 Windows 上运行这些应用程序的功能。

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/)归为 Microsoft 所有，可让你在中C#构建可靠的跨平台移动应用程序，类库和运行时可在多个平台（包括 IOS、Android 和 Windows）上运行，同时仍然编译本机（非解释）的应用程序。 Xamarin 结合了本机平台的所有功能，并添加了许多强大的功能。

- **编程语言**-C#
- **IDE** -Windows 或 Mac 上的 Visual Studio

### <a name="react-native"></a>React Native
由 Facebook 在2015中发布[，它是](https://facebook.github.io/react-native/)一个[开源](https://github.com/facebook/react-native)JavaScript 框架，用于编写面向 iOS 和 Android 的真实、本机呈现的移动应用程序。 它基于响应、Facebook 用于构建用户界面的 JavaScript 库，而不是面向浏览器，而是以移动平台为目标。 响应本机使用本机组件而不是 web 组件作为构建基块。
 
- **编程语言**-JavaScript
- **IDE** -Visual Studio Code

### <a name="unity"></a>Unity
 Unity 是一个为创建游戏而优化的引擎，开发人员可使用C#它为不同的平台（包括 Windows、IOS、Android 和 Xbox）创建高质量的 2D/3d 应用程序。

### <a name="cordova"></a>Cordova
Cordova 允许使用适用于 Apache Codova 的 Visual Studio Tools 或带有 Cordova 扩展的 Visual Studio Code 来构建混合应用程序。 利用混合方法，你可以与网站共享组件，并使用基于 Cordova 的托管 web 应用程序方法重复使用基于 web 服务器的应用程序。

#### <a name="pros"></a>优点
- 通过为多个平台创建一个基本代码来提高代码可用性
- 满足多个平台跨多个用户的需要
- 大大减少了开发时间
- 易于启动和更新

#### <a name="cons"></a>缺点
- 降低性能
- 缺乏灵活性
- 每个平台都具有一组独特的特性和功能，使本机应用程序更具创造性
- 增加 UI 设计时间
- 工具限制
