---
title: Azure 应用配置功能管理
description: 概述如何使用 Azure 应用程序配置来按需启用和禁用应用程序功能。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898732"
---
# <a name="feature-management-overview"></a>功能管理概述

一直以来，交付新的应用程序功能都需要完全重新部署该应用程序本身。 若要测试某项功能，可能需要多次部署应用程序，以控制何时显示该功能，或谁可以查看该功能。

功能管理是一种新式软件开发实践，它将功能发布与代码部署分离开来，可以按需快速更改功能的可用性。 它使用一种称作“功能标志”（也称为“功能切换”、“功能开关”等）的技术来动态管理功能的生命周期。

功能管理可以帮助开发人员处理以下问题：

* **代码分支管理**：功能标志经常用于包装正在开发的新应用程序功能。 此类功能默认是“隐藏的”。 不过，可以在此功能尚未完成的情况下交付它，它可以在生产环境中保持主导地位。 使用这种称作“暗处部署”的方法，可以在每个开发周期结束时发布所有代码。 不再需要在多个周期中维护任何代码分支，因为完成某项功能需要经历多个周期。
* **在生产环境中测试**：可以使用功能标志授予提前访问生产中的新功能的权限。 例如，可将此访问权限限制为团队成员或内部 Beta 测试人员。 这些用户将获得完全保真的生产体验，而无需在测试环境中使用模拟或部分体验。
* **试验**：还可以使用功能标志向最终用户逐渐推出新功能。 一开始可以面向少量的用户，在实施方面有了更大的自信心之后，可以逐渐提高目标用户百分比。
* **即时终止开关**：功能标志提供内置的安全网络用于发布新功能。 使用功能标志可以启用和禁用应用程序功能。 可根据需要快速禁用某项功能，而无需重新生成再重新部署应用程序。
* **选择性激活**：尽管大多数功能标志仅在其关联功能已成功发布之前存在，但有些标志可能会持续很长时间。 使用功能标志可以划分用户，并将特定的一组功能交付给每个组用户。 例如，某项功能只能在特定的 Web 浏览器中运行。 可以定义一个功能标志，以便只有运行该浏览器的用户能够查看和使用该功能。 使用此方法可以轻松扩展支持的浏览器列表，而无需进行任何代码更改。

## <a name="basic-concepts"></a>基本概念

下面是与功能管理相关的几个新术语：

* **功能标志**：功能标志是二进制状态为*on*或*off*的变量。 功能标志还具有关联的代码块。 无论代码块是否运行，都会触发功能标志的状态。
* **功能管理器**：功能管理器是一个应用程序包，用于处理应用程序中所有功能标志的生命周期。 功能管理器通常提供附加的功能，例如缓存功能标志和更新标志状态。
* **筛选**器：筛选器是用于评估功能标志的状态的规则。 用户组、设备或浏览器类型、地理位置和时间窗口都是可用作筛选依据的示例。

功能管理的有效实施方案至少包括两个协同工作的组件：

* 利用功能标志的应用程序。
* 用于存储功能标志及其当前状态的独立存储库。

以下示例演示了这些组件的交互方式。

## <a name="feature-flag-usage-in-code"></a>在代码中使用功能标志

在应用程序中实施功能标志的基本模式很简单。 可将功能标志视为与代码中的 `if` 条件语句配合使用的布尔值状态变量：

```csharp
if (featureFlag) {
    // Run the following code
}
```

在本例中，如果 `featureFlag` 设置为 `True`，则会执行包含的代码块；否则跳过该代码块。 可以静态设置 `featureFlag` 的值，如以下代码示例中所示：

```csharp
bool featureFlag = true;
```

也可以根据特定的规则评估标志的状态：

```csharp
bool featureFlag = isBetaUser();
```

稍微复杂一些的功能标志模式还包含 `else` 语句：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

但是，可在基本模式中重写此行为。 [在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)介绍了基于简单代码模式进行标准化的优点。 例如：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能标志存储库

若要有效使用功能标志，需要外部化应用程序中使用的所有功能标志。 使用此方法可以更改功能标志状态，而无需修改再重新部署应用程序本身。

Azure 应用程序配置旨在充当功能标志的集中式存储库。 可以使用它来定义不同类型的功能标志，并快速且有把握地操作其状态。 然后，可以使用适用于各种编程语言框架的应用程序配置库，轻松从应用程序访问这些功能标志。

[在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)介绍了如何结合使用 .NET Core 应用程序配置提供程序和功能管理库，针对 ASP.NET Web 应用程序实施功能标志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将功能标志添加到 ASP.NET Core Web 应用](./quickstart-feature-flag-aspnet-core.md)  
