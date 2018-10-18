---
title: Azure Migrate 中的依赖项可视化 | Microsoft 文档
description: 概述 Azure Migrate 服务中的评估计算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: 923a2a137bb4510e9490ce4077f744a43619a2c6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165018"
---
# <a name="dependency-visualization"></a>依赖项可视化

[Azure Migrate](migrate-overview.md) 服务会评估要迁移到 Azure 的本地计算机组。 可以在 Azure Migrate 中使用依赖项可视化功能来创建组。 本文提供了有关此功能的信息。


## <a name="overview"></a>概述

通过 Azure Migrate 中的依赖项可视化，可以为迁移评估创建高可信度组。 使用依赖项可视化，可以查看计算机的网络依赖项，并确定需要一起迁移到 Azure 的相关计算机。 在不完全了解构成应用程序并需要一起迁移到 Azure 的计算机的情况下，此功能非常有用。

## <a name="how-does-it-work"></a>工作原理

Azure Migrate 使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案来实现依赖项可视化。
- 要利用依赖项可视化功能，需要将现有或新的 Log Analytics 工作区与 Azure Migrate 项目进行关联。
- 只能在创建迁移项目的同一订阅中创建或附加工作区。
- 若要将 Log Analytics 工作区附加到项目，请转到项目“概述”页的“Essentials”部分，单击“需要配置”

    ![关联 Log Analytics 工作区](./media/concepts-dependency-visualization/associate-workspace.png)

- 创建新工作区时，需要指定工作区的名称。 然后在与迁移项目相同的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的区域内创建工作区。
- 关联的工作区使用“迁移项目”键和“项目名称”值进行标记，可用于在 Azure 门户中进行搜索。
- 若要导航到与项目关联的工作区，可以转到项目“概述”页的“Essentials”部分并访问工作区

    ![导航 Log Analytics 工作区](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用依赖项可视化，你需要在要分析的每台本地计算机上下载并安装代理。  

## <a name="do-i-need-to-pay-for-it"></a>是否需要对其付费？

无需额外付费即可使用 Azure Migrate。 使用 Azure Migrate 中的依赖项可视化功能需要服务映射，并且需要将现有或新的 Log Analytics 工作区与 Azure Migrate 项目相关联。 Azure Migrate 中的依赖项可视化功能在 Azure Migrate 中前 180 天内是免费的。

1. 在此 Log Analytics 工作区中使用服务映射以外的任何其他解决方案都会产生[标准 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 费用。
2. 为了不另外收费地支持迁移方案，服务映射解决方案在将 Log Analytics 工作区与 Azure Migrate 项目关联之日起的前 180 天内不会产生任何费用。 在 180 天之后，将收取标准 Log Analytics 费用。

将代理注册到工作区时，请在“安装代理步骤”页上使用项目提供的 ID 和密钥。

删除 Azure Migrate 项目时，工作区不会随之一起删除。 发布项目删除后，使用服务映射不会免费，每个节点将按照 Log Analytics 工作区的付费层进行收费。

> [!NOTE]
> 依赖项可视化功能通过 Log Analytics 工作区使用服务映射。 自 2018 年 2 月 28 日起，随着公告 Azure Migrate 正式发布，现在使用该功能无需额外付费。 你需要创建一个新项目，以便利用免费使用的工作区。 正式发布前的现有工作区仍收费，因此我们建议你迁移到新项目。

[在此处](https://azure.microsoft.com/pricing/details/azure-migrate/)详细了解 Azure Migrate 定价。

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作区？

你可以使用 Azure Migrate 外部的 Log Analytics 工作区。 如果删除在其中创建工作区的迁移项目，不会删除工作区。 如果你不再需要工作区，请手动[删除工作区](../log-analytics/log-analytics-manage-access.md)。

不要删除由 Azure Migrate 创建的工作区，除非你删除迁移项目。 否则，依赖项可视化功能将不会按预期工作。

## <a name="next-steps"></a>后续步骤
- [使用计算机依赖项分组计算机](how-to-create-group-machine-dependencies.md)
- [详细了解](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)依赖项可视化相关的常见问题解答。
