---
title: Microsoft Power BI Embedded 入门 | Microsoft Docs
description: 商业智能应用程序中的 Power BI Embedded
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 692edc9a685ce7d1906f13f08c763909a2513b03
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded 入门

使用 Power BI Embedded，独立软件供应商 (ISV) 和开发人员可以通过基于容量的按小时计算流量模型将令人惊叹的视觉对象、报告和仪表板快速添加到应用程序中。

![嵌入流关系图](media/get-started/introduction.png)

Power BI Embedded 为 ISV 及其开发人员和客户带来优势。 例如，ISV 可以开始免费使用 Power BI Desktop 创建视觉对象。 ISV 可以通过尽量减少视觉分析开发工作来加快入市速度，通过差异化的数据体验在竞争中脱颖而出。 ISV 还可以选择对通过嵌入式分析创建的附加价值收取额外费用。

开发人员可以将时间集中在增强应用程序的核心竞争力，而不是花在开发视觉对象和分析内容上。 开发人员可以快速满足客户的报表和仪表板需求，可以轻松地嵌入具有完整文档记录的 API 和 SDK。 最后，ISV 可以在其应用中启用易于导航的数据探索，使其客户可以根据任何设备的情况充满自信地进行快速的数据驱动型决策。

## <a name="register-an-application-within-azure-active-directory"></a>将应用程序注册到 Azure Active Directory 中

若要在自定义应用程序中进行嵌入，需在 Azure Active Directory (AAD) 中注册应用程序。 注册的应用程序要求租户为 Power BI 租户。 Power BI 租户意味着，组织中至少有一位用户已注册 Power BI。 用户注册 Power BI 后，Power BI API 就会显示在注册的应用程序中。

若要详细了解如何在 AAD 中注册应用程序，请参阅[注册 Azure AD 应用以便嵌入 Power BI 内容](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)。

## <a name="embed-content-in-your-application"></a>在应用程序中嵌入内容

在 AAD 中注册应用程序后，请在应用程序中嵌入 Power BI 内容。 将 REST API 与 JavaScript API 配合使用，以便嵌入内容。

我们提供相关示例，帮助你入门。 有关该示例的演练，请参阅[将仪表板、磁贴或报表集成到应用程序中](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)。

## <a name="get-capacity-and-move-to-production"></a>获取容量，进入生产

在 Microsoft Azure 中创建 Power BI Embedded 容量，使应用程序进入生产。 有关如何创建容量的信息，请参阅[在 Azure 门户中创建 Power BI Embedded 容量](create-capacity.md)。

> [!IMPORTANT]
> 由于嵌入令牌只应该用于开发测试，因此 Power BI 主帐户可以生成的嵌入令牌数是有限制的。 对于生产嵌入方案，[必须购买容量](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical)。 购买容量后，嵌入令牌的生成就没有限制。 转到[获取可用功能](https://msdn.microsoft.com/en-us/library/mt846473.aspx)检查当前嵌入使用情况（以百分比表示）。

在 Power BI 管理门户中管理容量。 分配用于应用工作区的工作区分配器。 有关详细信息，请参阅[管理 Power BI Premium 和 Power BI Embedded 中的容量](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)。

## <a name="next-steps"></a>后续步骤

若已准备好创建 Power BI Embedded 容量，请参阅[在 Azure 门户中创建 Power BI Embedded 容量](create-capacity.md)。

如需演练示例，请参阅[将仪表板、磁贴或报表集成到应用程序中](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)。

有更多问题？ [尝试 Power BI 社区](http://community.powerbi.com/)
