---
title: Azure Migrate 中的依赖项可视化 | Microsoft 文档
description: 概述 Azure Migrate 服务中的评估计算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: d075ce9ee124d373a1284577324a50338245a03f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="dependency-visualization"></a>依赖项可视化

[Azure Migrate](migrate-overview.md) 服务会评估要迁移到 Azure 的本地计算机组。 可以使用依赖项可视化将计算机组合在一起。 本文提供了有关此功能的信息。


## <a name="overview"></a>概述

通过 Azure Migrate 中的依赖项可视化，你可以更有把握地创建迁移评估的组。 使用依赖项可视化，你可以查看特定计算机或一组计算机内的网络依赖项。 这将有助于在应用和工作负载跨多台计算机运行时，避免在迁移过程中出现功能不起作用或丢失（或计算机被遗忘）的情况。  

## <a name="how-does-it-work"></a>工作原理

Azure Migrate 使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案来实现依赖项可视化。
- 创建 Azure 迁移项目时，会在你的订阅中创建一个 Log Analytics 工作区。
- 工作区名称是你为迁移项目指定的名称，以 **migrate-** 为前缀，并且（可选）以数字为后缀。 
- 从项目“概述”页的“概要”部分导航到 Log Analytics 工作区。
- 创建的工作区使用密钥 **MigrateProject** 和值“项目名称”进行标记。 你可以使用这些信息在 Azure 门户中进行搜索。  

    ![Log Analytics 工作区](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用依赖项可视化，你需要在要分析的每台本地计算机上下载并安装代理。  

## <a name="do-i-need-to-pay-for-it"></a>是否需要对其付费？

无需额外付费即可使用 Azure Migrate。 在 Azure Migrate 中使用依赖项可视化功能需要服务映射。 创建 Azure Migrate 项目时，Azure Migrate 会代表你自动创建一个新的 Log Analytics 工作区。

> [!NOTE]
> 依赖项可视化功能通过 Log Analytics 工作区使用服务映射。 自 2018 年 2 月 28 日起，随着公告 Azure Migrate 正式发布，现在使用该功能无需额外付费。 你需要创建一个新项目，以便利用免费使用的工作区。 正式发布前的现有工作区仍收费，因此我们建议你迁移到新项目。

1. 在此 Log Analytics 工作区中使用服务映射以外的任何其他解决方案都会产生标准 Log Analytics 费用。 
2. 为了不另外收费地支持迁移方案，服务映射解决方案将在从创建 Azure Migrate 项目起的前 180 天内不产生任何费用，在此之后将收取标准费用。
3. 只有在创建项目的过程中创建的工作区可免费使用。

将代理注册到工作区时，请在“安装代理步骤”页上使用项目提供的 ID 和密钥。 不能使用现有工作区，也不能将其与 Azure Migrate 项目相关联。

删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除该项目后，使用服务映射不会免费，每个节点将按照 Log Analytics 工作区的付费层进行收费。

[在此处](https://azure.microsoft.com/pricing/details/azure-migrate/)详细了解 Azure Migrate 定价。 

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作区？

你可以使用 Azure Migrate 外部的 Log Analytics 工作区。 如果删除在其中创建工作区的迁移项目，不会删除工作区。 如果你不再需要工作区，请手动[删除工作区](../log-analytics/log-analytics-manage-access.md)。

不要删除由 Azure Migrate 创建的工作区，除非你删除迁移项目。 如果删除，则依赖项将无法按预期方式工作。

## <a name="next-steps"></a>后续步骤

[使用计算机依赖项分组计算机](how-to-create-group-machine-dependencies.md)