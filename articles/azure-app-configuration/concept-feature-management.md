---
title: 使用 Azure 应用程序配置了解功能管理
description: 使用 Azure 应用程序配置打开和关闭功能
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b77f0063b37adbfaecaff68387e858d0077561b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212664"
---
# <a name="feature-management-overview"></a>功能管理概述

一直以来，交付新的应用程序功能都需要完全重新部署该应用程序本身。 测试功能通常需要应用程序的多个部署。  每个部署可能会更改该功能或将该功能公开给不同的客户进行测试。  

功能管理是一种新式软件开发实践，它将功能发布与代码部署分离开来，可以按需快速更改功能的可用性。 它使用一种称作“功能标志”（也称为“功能切换”、“功能开关”等）的技术来动态管理功能的生命周期。******

功能管理可以帮助开发人员处理以下问题：

* **代码分支管理**：使用功能标志来包装当前正在开发中的新应用程序功能。 此类功能默认是“隐藏的”。 不过，可以在此功能尚未完成的情况下交付它，它可以在生产环境中保持主导地位。 使用这种称作“暗处部署”的方法，可以在每个开发周期结束时发布所有代码。** 你不再会因为给定功能需要多个周期才能完成而需要在多个开发周期中维护代码分支。
* **在生产环境中测试**：在生产环境中，使用功能标志提前授予对新功能的访问权限。 例如，可将访问权限限制为团队成员或内部 Beta 测试人员。 这些用户将获得完全保真的生产体验，而无需在测试环境中使用模拟或部分体验。
* **外部测试**：使用功能标志以增量方式将新功能推出给最终用户。 一开始可以面向少量的用户，然后逐渐提高目标用户百分比。
* **即时终止开关**：功能标志提供一个固有的安全网络用于发布新功能。 可以打开和关闭应用程序功能，而无需重新部署任何代码。 可根据需要快速禁用某项功能，而无需重新生成再重新部署应用程序。
* **选择性激活**：使用功能标志划分用户，并将特定的一组功能交付给每个组用户。 可能有某项功能只能在特定的 Web 浏览器中运行。 可以定义一个功能标志，以便只有运行该浏览器的用户能够查看和使用该功能。 使用此方法可以轻松扩展支持的浏览器列表，而无需进行任何代码更改。

## <a name="basic-concepts"></a>基本概念

下面是与功能管理相关的几个新术语：

* **功能标志**：功能标志是具有 *on* 或 *off* 二进制状态的变量。 功能标志还具有关联的代码块。 无论代码块是否运行，都会触发功能标志的状态。
* **功能管理器**：功能管理器是一个应用程序包，可以处理应用程序中所有功能标志的生命周期。 功能管理器还提供附加的功能，包括缓存功能标志和更新标志状态。
* **筛选器**：筛选器是用于评估功能标志状态的规则。 潜在筛选器包括用户组、设备或浏览器类型、地理位置和时间窗口。

功能管理的有效实施方案至少包括两个协同工作的组件：

* 利用功能标志的应用程序。
* 用于存储功能标志及其当前状态的独立存储库。

## <a name="using-feature-flags-in-your-code"></a>在代码中使用功能标志

在应用程序中实施功能标志的基本模式很简单。 功能标志是控制代码中条件语句的布尔值状态变量：

```csharp
if (featureFlag) {
    // Run the following code
}
```

可以静态设置 `featureFlag` 的值。

```csharp
bool featureFlag = true;
```

可以根据特定的规则评估标志的状态：

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

若要有效使用功能标志，需要外部化应用程序中使用的所有功能标志。 这样，你就可以更改功能标志状态，而无需修改再重新部署应用程序本身。

Azure 应用程序配置提供功能标志的集中存储库。 可以使用它来定义不同类型的功能标志，并快速且有把握地操作其状态。 然后，可以使用适用于各种编程语言框架的应用程序配置库，轻松从应用程序访问这些功能标志。

[在 ASP.NET Core 应用中使用功能标志](./use-feature-flags-dotnet-core.md)介绍了如何结合使用 .NET Core 应用程序配置提供程序和功能管理库，针对 ASP.NET Web 应用程序实施功能标志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将功能标志添加到 ASP.NET Core Web 应用](./quickstart-feature-flag-aspnet-core.md)  
