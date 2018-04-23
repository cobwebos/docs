---
title: 什么是 Power BI 工作区集合？
description: Power BI Embedded 使你可将 Power BI 报表集成到 Web 或移动应用程序中，因此无需构建自定义解决方案。
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 0bd6ba74d176ed1db1e8f1f1b38646182da2c379
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="what-are-power-bi-workspace-collections"></a>什么是 Power BI 工作区集合？

通过 **Power BI 工作区集合**，可以将 Power BI 报表集成到 Web 或移动应用程序。

![应用程序关系图](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

Power BI 工作区集合是一项 **Azure 服务**，使 ISV 和应用程序开发人员能够在其自己的应用程序中融入 Power BI 数据体验。 开发人员已经构建了应用程序，并且这些应用程序具有其自己的用户和不同的功能集。 这些应用也可能碰巧具有内置的数据元素（如图表和报表），这些元素现在可以由 Microsoft Power BI 工作区集合提供支持。 不需要具有 Power BI 帐户便可使用应用。 可以继续像以前那样登录到这些应用程序，查看并与 Power BI 报表体验进行交互而无需任何额外的授权。

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>针对 Microsoft Power BI 工作区集合进行授权

在 **Microsoft Power BI 工作区集合**使用模型中，Power BI 的授权不是由最终用户负责的。  相反，应该由使用视觉对象的应用开发人员来购买**会话**，费用计入拥有这些资源的订阅中。 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI 工作区集合概念模型

![包含工作区集合的应用程序流](media/what-are-power-bi-workspace-collections/model.png)

与 Azure 中的任何其他服务一样，Power BI 工作区集合的资源也是通过 [Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx) 进行预配的。 在这种情况下，预配的资源是一个 **Power BI 工作区集合**。

## <a name="workspace-collection"></a>工作区集合

**工作区集合**是一个顶级 Azure 资源容器，包含 0 个或多个**工作区**。  **工作区****集合**具有所有标准 Azure 属性和下列内容：

* **访问密钥** - 安全地调用 Power BI API 时使用的密钥（后面的部分中进行了介绍）。
* **用户** - 具有管理员权限的 Azure Active Directory (AAD) 用户，通过 Azure 门户或 Azure 资源管理器 API 管理 Power BI 工作区集合。
* **区域** - 在预配**工作区集合**的过程中，可以选择要在其中预配的区域。 有关详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

## <a name="workspace"></a>工作区

**工作区**是一个包含 Power BI 内容的容器，该内容可以包括数据集和报表。 **工作区**在首次创建后为空。 将使用 Power BI Desktop 创作内容，然后使用 [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx) 以编程方式将 PBIX 部署到工作区中。 还可以通过编程方式创建数据集，并在你的应用程序内而非使用 Power BI Desktop 创建报表。

## <a name="using-workspace-collections-and-workspaces"></a>使用工作区集合和工作区

**工作区集合**和**工作区**是内容容器，它们按最适合所生成应用程序设计的方式进行使用和组织。 它们提供了许多不同的方式供用户在其中排列内容。 可以选择将所有内容都放在一个工作区中并在稍后使用应用令牌进一步细分客户之间的内容。 还可以选择将所有客户放在单独的工作区中，使他们分开一些。 另外，也可以选择按区域而不是按客户来组织用户。 这种设计十分灵活，可以选择最佳的方式来组织内容。

## <a name="cached-datasets"></a>缓存的数据集

可以使用缓存的数据集。  但是，将缓存数据加载到 **Microsoft Power BI 工作区集合**之后将无法刷新该数据。 缓存的数据集意味着已将数据导入到 Power BI Desktop 中而非使用 DirectQuery。

## <a name="authentication-and-authorization-with-app-tokens"></a>使用应用令牌进行身份验证和授权

**Microsoft Power BI 工作区集合**遵从应用程序来执行所有必要的用户身份验证和授权。 没有明确要求最终用户必须是 Azure Active directory (Azure AD) 的客户。  相反，应用程序通过使用**应用程序身份验证令牌（应用令牌）**向 **Microsoft Power BI 工作区集合**表明自己获得了呈现 Power BI 报表的授权。  希望应用呈现报表时，可以根据需要创建这些**应用令牌**。

![应用令牌使用情况图表](media/what-are-power-bi-workspace-collections/app-tokens.png)

**应用程序身份验证令牌（应用令牌）**用于向 **Microsoft Power BI 工作区集合**表明身份。  有三种类型的**应用令牌**：

1. 预配令牌 - 在**工作区集合**中预配新的**工作区**时使用
2. 开发令牌 - 直接调用 **Power BI REST API** 时使用
3. 嵌入令牌 - 进行调用以在嵌入式 iframe 中呈现报表时使用

这些令牌适用于与 **Microsoft Power BI 工作区集合**进行交互的各种阶段。  这些令牌经过专门设计，以便可以将应用中的权限委托给 Power BI。 有关详细信息，请参阅[应用令牌流](app-token-flow.md)。

## <a name="create-or-edit-reports-within-your-application"></a>在应用程序内创建或编辑报表

现在，可以直接在应用程序中编辑现有报表或创建新报表，而非必须使用 Power BI Desktop。 这要求数据集存在于工作区中。

## <a name="see-also"></a>另请参阅

[常见 Microsoft Power BI 工作区集合方案](scenarios.md)  
[Microsoft Power BI 工作区集合入门](get-started.md)  
[示例入门](get-started-sample.md)  
[嵌入报表](embed-report.md)  
[在 Power BI 工作区集合中进行身份验证和授权](app-token-flow.md)  
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git 存储库](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 存储库](https://github.com/Microsoft/PowerBI-Node)  

有更多问题？ [尝试 Power BI 社区](http://community.powerbi.com/)
