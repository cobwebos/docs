---
title: 选择用于通过 Visual Studio 和 Azure 服务生成客户端应用程序的前端开发平台
description: 了解用于生成客户端应用程序的受支持的本机和跨平台语言。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453157"
---
# <a name="choose-mobile-development-frameworks"></a>选择移动开发框架
开发人员可以通过对跨平台方法使用特定的框架和模式，使用客户端技术自行构建移动应用程序。 根据其决策因素，开发人员可以生成：
- 使用目标 C 和 Java 等语言的本机单平台应用程序
- 使用 Xamarin、.NET 和的跨平台应用程序C#
- 使用 Cordova 及其变体的混合应用程序

## <a name="native-platforms"></a>本机平台
构建本机应用程序需要使用特定于平台的编程语言、Sdk、开发环境和操作系统供应商提供的其他工具。

### <a name="ios"></a>iOS
IOS 由 Apple 创建和开发，用于在 Apple 设备（即 iPhone 和 iPad）上生成应用。

- **编程语言**：目标-C、Swift
- **IDE**： Xcode
- **SDK**： iOS sdk

### <a name="android"></a>Android
Android 是由 Google 和世界上最常用的操作系统设计的，它用于构建可在众多智能手机和平板电脑上运行的应用程序。

- **编程语言**： Java、Kotlin 
- **IDE**： Android Studio 和 Android 开发人员工具 
- **SDK**： Android SDK

### <a name="windows"></a>Windows
- **编程语言**：C#
- **IDE**： Visual Studio，Visual Studio Code
- **SDK**： Windows SDK

#### <a name="pros"></a>优点
- 良好的用户体验
- 具有高性能和可与本机库交互的功能的响应式应用程序
- 高度安全的应用程序

#### <a name="cons"></a>缺点
- 应用程序只在一个平台上运行
- 构建应用程序的开发人员消耗更多资源
- 更低的代码重用

## <a name="cross-platforms-and-hybrid-applications"></a>跨平台和混合应用程序
跨平台应用程序可让你一次编写本机移动应用程序、共享代码并在 iOS、Android 和 Windows 上运行。

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/)由 Microsoft 所有，用于在中C#构建可靠的跨平台移动应用程序。 Xamarin 具有可在许多平台（如 iOS、Android 和 Windows）上运行的类库和运行时。 它还编译提供高性能的本机（非解释）应用程序。 Xamarin 结合了本机平台的所有功能，并添加了许多强大的功能。

- **编程语言**：C#
- **IDE**： Windows 或 Mac 上的 Visual Studio

### <a name="react-native"></a>React Native
由 Facebook 在2015中发布[，它](https://facebook.github.io/react-native/)是一个[开源](https://github.com/facebook/react-native)JavaScript 框架，用于编写面向 iOS 和 Android 的真实、本机呈现的移动应用程序。 它基于响应，这是用于构建用户界面的 Facebook JavaScript 库。 它以移动平台为目标，而不是面向浏览器。 响应本机使用本机组件而不是 web 组件作为构建基块。
 
- **编程语言**： JavaScript
- **IDE**： Visual Studio Code

### <a name="unity"></a>Unity
 Unity 是一个为创建游戏而优化的引擎。 可以使用它来创建C#适用于 Windows、IOS、Android 和 Xbox 等平台的高质量2D 或3d 应用程序。

### <a name="cordova"></a>Cordova
Cordova 允许使用 Apache Cordova 的 Visual Studio Tools 或带有 Cordova 扩展的 Visual Studio Code 来构建混合应用程序。 利用混合方法，你可以与网站共享组件，并使用基于 Cordova 的托管 web 应用程序方法重用基于 web 服务器的应用程序。

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
