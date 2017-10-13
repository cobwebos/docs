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
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e7acb9fbddc59803ed8cf6f1d7dcda0171998f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-case---customer-profiling"></a>用例 - 客户分析
Azure 数据工厂是用于实现解决方案加速器的 Cortana Intelligence 套件的许多服务之一。  有关 Cortana Intelligence 的详细信息，请访问 [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics)（Cortana Intelligence 套件）。 此文档介绍一个简单的用例，帮助你了解 Azure 数据工厂解决常见分析问题的方法。

## <a name="scenario"></a>方案
Contoso 是为多个平台（游戏控制台、手持设备和个人计算机 (PC)）创建游戏的游戏公司。 玩家玩这些游戏的过程中将生成大量日志数据，这些日志数据可跟踪使用模式、游戏风格和用户首选项。  与人口、区域和产品数据相结合后，Contoso 可执行分析，以便指导如何改善玩家的体验，以及升级和游戏内购买。 

Contoso 的目标是基于其玩家的游戏历史记录确定向上销售/交叉销售机会，添加极具吸引力的功能以促进企业发展，以及为客户提供更好的体验。 有关此用例，我们将使用游戏公司作为企业的示例。 该公司希望基于玩家的行为优化其游戏。 这些原则适用于想要使用其产品和服务吸引客户并增强其客户体验的任何企业。

在此解决方案中，Contoso 需要评估其最近启动的市场营销活动的成效。 我们从原始游戏日志着手，对其进行处理并使用地理位置数据将其扩充，加入广告引用数据，最后将其复制到 Azure SQL 数据库，进而分析市场活动的影响。

## <a name="deploy-solution"></a>部署解决方案
访问和试用此简单用例所需要的是 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)、[Azure Blob 存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)和 [Azure SQL 数据库](../../sql-database/sql-database-get-started.md)。 在数据工厂主页上的“示例管道”磁贴上，部署客户分析管道。

1. 创建数据工厂或打开现有数据工厂。 有关创建数据工厂的步骤，请参阅[使用数据工厂将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
2. 在数据工厂的“数据工厂”边栏选项卡中，单击“示例管道”磁贴。

    ![“示例管道”磁贴](./media/data-factory-samples/SamplePipelinesTile.png)
3. 在“示例管道”边栏选项卡中，单击要部署的“客户分析”。

    ![“示例管道”边栏选项卡](./media/data-factory-samples/SampleTile.png)
4. 指定此示例的配置设置。 例如，Azure 存储帐户名和密钥、Azure SQL server 名称、数据库、用户 ID 和密码。

    ![“示例”边栏选项卡](./media/data-factory-samples/SampleBlade.png)
5. 指定配置设置后，单击“创建”可以创建/部署示例管道和此管道使用的链接服务/表格。
6. 可在之前在“示例管道”边栏选项卡上单击的“示例”磁贴上看到部署状态。

    ![部署状态](./media/data-factory-samples/DeploymentStatus.png)
7. 在此示例的磁贴上看到“已成功部署”消息时，关闭“示例管道”边栏选项卡。  
8. 在“数据工厂”边栏选项卡上，将看到链接服务、数据集和管道已添加到数据工厂。  

    ![“数据工厂”边栏选项卡](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>解决方案概述
此简单用例可用作如何使用 Azure 数据工厂来引入、准备、转换、分析和发布数据的示例。

![端到端工作流](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

此图描述了部署数据管道后，数据管道在 Azure 门户中的显示方式。

1. **PartitionGameLogsPipeline** 从 blob 存储中读取原始游戏事件，并创建基于年、月和日的分区。
2. **EnrichGameLogsPipeline** 联接分区的游戏事件与地区代码参考数据，并通过将 IP 地址映射到相应的地理位置来丰富数据。
3. **AnalyzeMarketingCampaignPipeline** 管道使用丰富的数据，并通过广告数据对其进行处理，以创建包含营销活动效果的最终输出。

在此示例中，数据工厂用于安排活动（复制输入数据、转换和处理数据，并将最终数据输出到 Azure SQL 数据库）。  还可以对数据管道的网络进行可视化、对其进行管理，以及从 UI 监视其状态。

## <a name="benefits"></a>优点
通过优化其用户配置文件分析并将其与业务目标匹配，游戏公司可快速收集使用模式并分析其营销活动的效果。

