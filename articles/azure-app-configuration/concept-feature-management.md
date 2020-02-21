---
title: 使用 Azure 应用配置了解功能管理
description: 使用 Azure 应用配置打开和关闭功能
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523724"
---
# <a name="feature-management-overview"></a>功能管理概述

一直以来，交付新的应用程序功能都需要完全重新部署该应用程序本身。 测试功能通常需要多个应用程序部署。  每个部署可能会更改功能或向不同客户公开此功能以便进行测试。  

功能管理是一种新式软件开发实践，它将功能发布与代码部署分离开来，可以按需快速更改功能的可用性。 它使用一种称作“功能标志”（也称为“功能切换”、“功能开关”等）的技术来动态管理功能的生命周期。

功能管理可帮助开发人员解决以下问题：

* **代码分支管理**：使用功能标志来包装当前正在开发的新应用程序功能。 此类功能默认是“隐藏的”。 不过，可以在此功能尚未完成的情况下交付它，它可以在生产环境中保持主导地位。 使用这种称作“暗处部署”的方法，可以在每个开发周期结束时发布所有代码。 你不再需要跨多个开发周期维护代码分支，因为给定的功能需要多个循环才能完成。
* **在生产环境中测试**：使用功能标志授予提前访问生产中的新功能的权限。 例如，你可以限制对团队成员或内部 beta 测试人员的访问权限。 这些用户将体验到完全保真生产体验，而不是在测试环境中使用模拟或部分经验。
* **试验**：使用功能标志以增量方式向最终用户推出新功能。 您可以先针对少量的用户填充，并随着时间的推移逐渐增加该百分比。
* **即时终止开关**：功能标志提供内置的安全网络用于发布新功能。 可以打开和关闭应用程序功能，而无需重新部署任何代码。 可根据需要快速禁用某项功能，而无需重新生成再重新部署应用程序。
* **选择性激活**：使用功能标志对用户进行分段，并向每个组提供一组特定功能。 你的功能可能仅适用于特定的 web 浏览器。 可以定义一个功能标志，以便只有运行该浏览器的用户能够查看和使用该功能。 使用此方法可以轻松扩展支持的浏览器列表，而无需进行任何代码更改。

## <a name="basic-concepts"></a>基本概念

下面是与功能管理相关的几个新术语：

* **功能标志**：功能标志是二进制状态为*on*或*off*的变量。 功能标志还具有关联的代码块。 功能标志的状态将触发代码块是否运行。
* **功能管理器**：功能管理器是一个应用程序包，用于处理应用程序中所有功能标志的生命周期。 功能管理器还提供附加功能，包括缓存功能标志并更新其状态。
* **筛选**器：筛选器是用于评估功能标志的状态的规则。 潜在筛选器包括用户组、设备或浏览器类型、地理位置和时间窗口。

功能管理的有效实施方案至少包括两个协同工作的组件：

* 利用功能标志的应用程序。
* 用于存储功能标志及其当前状态的独立存储库。

## <a name="using-feature-flags-in-your-code"></a>在代码中使用功能标志

在应用程序中实施功能标志的基本模式很简单。 功能标志是在代码中控制条件语句的布尔状态变量：

```csharp
if (featureFlag) {
    // Run the following code
}
```

可以静态设置 `featureFlag` 的值。

```csharp
bool featureFlag = true;
```

你可以根据某些规则评估标志的状态：

```csharp
bool featureFlag = isBetaUser();
```

可以扩展条件以设置任一状态的应用程序行为：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能标志存储库

若要有效使用功能标志，需要外部化应用程序中使用的所有功能标志。 这使你可以更改功能标志状态，而无需修改和重新部署应用程序本身。

Azure 应用配置提供了一个用于功能标志的集中式存储库。 可以使用它来定义不同类型的功能标志，并快速且有把握地操作其状态。 然后，可以使用适用于各种编程语言框架的应用程序配置库，轻松从应用程序访问这些功能标志。

[在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)介绍了如何结合使用 .NET Core 应用程序配置提供程序和功能管理库，针对 ASP.NET Web 应用程序实施功能标志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将功能标志添加到 ASP.NET Core Web 应用](./quickstart-feature-flag-aspnet-core.md)  
