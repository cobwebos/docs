---
title: 使用 Azure 应用配置了解功能管理
description: 使用 Azure 应用配置打开和关闭功能
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523724"
---
# <a name="feature-management-overview"></a>功能管理概述

一直以来，交付新的应用程序功能都需要完全重新部署该应用程序本身。 测试功能通常需要应用程序的多个部署。  每个部署可能会更改功能或向不同的客户公开该功能以进行测试。  

功能管理是一种新式软件开发实践，它将功能发布与代码部署分离开来，可以按需快速更改功能的可用性。 它使用一种称作“功能标志”（也称为“功能切换”、“功能开关”等）的技术来动态管理功能的生命周期。******

功能管理可帮助开发人员解决以下问题：

* **代码分支管理**：使用功能标志包装当前开发的新应用程序功能。 此类功能默认是“隐藏的”。 不过，可以在此功能尚未完成的情况下交付它，它可以在生产环境中保持主导地位。 使用这种称作“暗处部署”的方法，可以在每个开发周期结束时发布所有代码。** 您不再需要跨多个开发周期维护代码分支，因为给定功能需要多个周期才能完成。
* **在生产中测试**：使用功能标志允许早期访问生产中的新功能。 例如，您可以限制对团队成员或内部 beta 测试人员的访问。 这些用户将在测试环境中体验全保真生产体验，而不是模拟或部分体验。
* **飞行**：使用功能标志向最终用户逐步推出新功能。 您可以首先定位一小部分用户群，并随着时间的推移逐渐增加该百分比。
* **即时杀伤开关**：功能标志为发布新功能提供了一个固有的安全网。 无需重新部署任何代码即可打开和关闭应用程序功能。 可根据需要快速禁用某项功能，而无需重新生成再重新部署应用程序。
* **选择性激活**：使用功能标志对用户进行分段，并将一组特定的功能交付给每个组。 您可能具有仅在特定 Web 浏览器上工作的功能。 可以定义一个功能标志，以便只有运行该浏览器的用户能够查看和使用该功能。 使用此方法可以轻松扩展支持的浏览器列表，而无需进行任何代码更改。

## <a name="basic-concepts"></a>基本概念

下面是与功能管理相关的几个新术语：

* **特征标志**：要素标志是二进制状态为*开**或关*的变量。 功能标志还具有关联的代码块。 要素标志的状态触发代码块是否运行。
* **功能管理器**：功能管理器是处理应用程序中所有功能标志的生命周期的应用程序包。 功能管理器还提供其他功能，包括缓存功能标志并更新其状态。
* **筛选器**：筛选器是用于评估要素标志状态的规则。 潜在筛选器包括用户组、设备或浏览器类型、地理位置和时间窗口。

功能管理的有效实施方案至少包括两个协同工作的组件：

* 利用功能标志的应用程序。
* 用于存储功能标志及其当前状态的独立存储库。

## <a name="using-feature-flags-in-your-code"></a>在代码中使用功能标志

在应用程序中实施功能标志的基本模式很简单。 特征标志是控制代码中条件语句的布尔状态变量：

```csharp
if (featureFlag) {
    // Run the following code
}
```

您可以静态设置`featureFlag`值。

```csharp
bool featureFlag = true;
```

您可以根据某些规则评估标志的状态：

```csharp
bool featureFlag = isBetaUser();
```

您可以扩展条件以为任一状态设置应用程序行为：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能标志存储库

若要有效使用功能标志，需要外部化应用程序中使用的所有功能标志。 这允许您更改功能标志状态，而无需修改和重新部署应用程序本身。

Azure 应用配置为功能标志提供了集中式存储库。 可以使用它来定义不同类型的功能标志，并快速且有把握地操作其状态。 然后，可以使用适用于各种编程语言框架的应用程序配置库，轻松从应用程序访问这些功能标志。

[在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)介绍了如何结合使用 .NET Core 应用程序配置提供程序和功能管理库，针对 ASP.NET Web 应用程序实施功能标志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将功能标志添加到 ASP.NET Core Web 应用](./quickstart-feature-flag-aspnet-core.md)  
