---
title: "用例 - 客户分析"
description: "了解如何使用 Azure 数据工厂来创建数据驱动的工作流（管道），以分析游戏客户。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a3eb126aa508204becbebb3dd763bdfde5890c8a


---
# <a name="use-case---customer-profiling"></a>用例 - 客户分析
Azure 数据工厂是用于实现解决方案加速器的 Cortana Intelligence 套件的许多服务之一。  有关 Cortana Intelligence 的详细信息，请访问 [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics)（Cortana Intelligence 套件）。 此文档将介绍一个简单的用例，帮助你了解 Azure 数据工厂解决常见分析问题的方法。

访问和试用此简单用例仅需要 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)。  可以按照[示例](data-factory-samples.md)文章中所述的步骤部署实现此用例的示例。

## <a name="scenario"></a>方案
Contoso 是为多个平台（游戏控制台、手持设备和个人计算机 (PC)）创建游戏的游戏公司。 玩家玩这些游戏的过程中将生成大量日志数据，这些日志数据可跟踪使用模式、游戏风格和用户首选项。  与人口、区域和产品数据相结合后，Contoso 可执行分析，以便指导如何改善玩家的体验，以及升级和游戏内购买。 

Contoso 的目标是基于其玩家的游戏历史记录确定向上销售/交叉销售机会，添加极具吸引力的功能以促进企业发展，以及为客户提供更好的体验。 有关此用例，我们将使用游戏公司作为企业的示例。 该公司希望基于玩家的行为优化其游戏。 这些原则适用于想要使用其产品和服务吸引客户并增强其客户体验的任何企业。

## <a name="challenges"></a>挑战
## <a name="solution-overview"></a>解决方案概述
此简单用例可用作如何使用 Azure 数据工厂来引入、准备、转换、分析和发布数据的示例。

![端到端工作流](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

此图描述了部署数据管道后，数据管道在 Azure 门户中的显示方式。

1. **PartitionGameLogsPipeline** 从 blob 存储中读取原始游戏事件，并创建基于年、月和日的分区。
2. **EnrichGameLogsPipeline** 联接分区的游戏事件与地区代码参考数据，并通过将 IP 地址映射到相应的地理位置来丰富数据。
3. **AnalyzeMarketingCampaignPipeline** 管道使用丰富的数据，并通过广告数据对其进行处理，以创建包含营销活动效果的最终输出。

在此示例中，数据工厂将用于安排活动（复制输入数据、转换和处理数据，并将最终数据输出到 Azure SQL 数据库）。  还可以对数据管道的网络进行可视化、对其进行管理，以及从 UI 监视其状态。

## <a name="benefits"></a>优点
通过优化其用户配置文件分析并将其与业务目标匹配，游戏公司可快速收集使用模式并分析其营销活动的效果。




<!--HONumber=Nov16_HO3-->


