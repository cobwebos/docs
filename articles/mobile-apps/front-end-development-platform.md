---
title: 选择前端开发平台，使用 Visual Studio 和 Azure 服务构建客户端应用程序
description: 了解支持本机和跨平台语言以构建客户端应用程序。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240860"
---
# <a name="choose-mobile-development-frameworks"></a>选择移动开发框架
开发人员可以使用客户端技术来构建移动应用程序，方法是使用跨平台方法的特定框架和模式。 根据他们的决策因素，开发人员可以构建：
- 通过使用目标 C 和 Java 等语言实现本机单平台应用程序
- 使用 Xamarin、.NET 和 C 的跨平台应用程序#
- 使用科尔多瓦及其变体的混合应用

## <a name="native-platforms"></a>本机平台
构建本机应用程序需要特定于平台的编程语言、SDK、开发环境以及操作系统供应商提供的其他工具。

### <a name="ios"></a>iOS
iOS由苹果创建和开发，用于在苹果设备上构建应用程序，即iPhone和iPad。

- **编程语言**： 目标 C， Swift
- **IDE**： Xcode
- **SDK**： iOS SDK

### <a name="android"></a>Android
Android由谷歌和世界上最流行的操作系统设计，用于构建可在一系列智能手机和平板电脑上运行的应用程序。

- **编程语言**： Java， 科特林 
- **IDE**： Android 工作室和 Android 开发人员工具 
- **SDK**： 安卓 SDK

### <a name="windows"></a>Windows
- **编程语言**： C#
- **IDE**： 视觉工作室， 视觉工作室代码
- **SDK**： Windows SDK

#### <a name="pros"></a>优点
- 良好的用户体验
- 具有高性能且能够与本机库接口的响应式应用程序
- 高度安全的应用程序

#### <a name="cons"></a>缺点
- 应用程序仅在一个平台上运行
- 构建应用程序占用更多开发人员资源且成本高昂
- 降低代码重用率

## <a name="cross-platforms-and-hybrid-applications"></a>跨平台和混合应用
跨平台应用程序使您能够编写本机移动应用程序一次，共享代码，并在 iOS、Android 和 Windows 上运行它们。

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/)由 Microsoft 拥有，用于在 C# 中构建强大的跨平台移动应用程序。 Xamarin 具有一个类库和运行时，可跨许多平台（如 iOS、Android 和 Windows）工作。 它还编译提供高性能的本机（非解释）应用程序。 Xamarin 结合了本机平台的所有功能，并增加了其自身的许多强大功能。

- **编程语言**： C#
- **IDE**： 视窗或 Mac 上的可视化工作室

### <a name="react-native"></a>React Native
由 Facebook 于 2015 年发布[，React Native](https://facebook.github.io/react-native/)是一个[开源](https://github.com/facebook/react-native)JavaScript 框架，用于编写适用于 iOS 和 Android 的真实本地呈现移动应用程序。 它基于 React，Facebook 的 JavaScript 库，用于构建用户界面。 它不是针对浏览器，而是针对移动平台。 React 本机使用本机组件而不是 Web 组件作为构建基块。
 
- **编程语言**：JavaScript
- **IDE**： 视觉工作室代码

### <a name="unity"></a>Unity
 Unity 是专为创建游戏而优化的引擎。 您可以使用它为 Windows、iOS、Android 和 Xbox 等平台使用 C# 制作高质量的 2D 或 3D 应用程序。

### <a name="cordova"></a>Cordova
Cordova 允许您使用适用于 Apache Cordova 的视觉工作室工具或带有科尔多瓦扩展功能的视觉工作室代码构建混合应用程序。 使用混合方法，您可以与网站共享组件，并将基于 Cordova 的托管 Web 应用程序方法重用基于 Web 服务器的应用程序。

#### <a name="pros"></a>优点
- 通过为多个平台创建一个代码库，提高代码可用性
- 跨多个平台面向更广泛的受众
- 显著缩短开发时间
- 易于启动和更新

#### <a name="cons"></a>缺点
- 性能更低
- 缺乏灵活性
- 每个平台都有一组独特的特性和功能，使本机应用程序更具创意
- 增加 UI 设计时间
- 工具限制
