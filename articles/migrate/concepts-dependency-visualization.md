---
title: Azure Migrate 中的依赖项可视化 | Microsoft 文档
description: 概述中的服务器评估服务中的评估计算 Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 8934306efadc4ec732afbb658c081ada30f232cd
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312210"
---
# <a name="dependency-visualization"></a>依赖项可视化

Azure Migrate：服务器评估将评估要迁移到 Azure 的本地计算机组。 您可以使用服务器评估中的依赖项可视化功能来创建组。 本文提供了有关此功能的信息。

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="overview"></a>概述

服务器评估中的依赖关系可视化允许您为迁移评估创建高可信度组。 使用依赖关系可视化, 你可以查看计算机的网络依赖关系, 并确定需要一起迁移到 Azure 的相关计算机。 在不完全了解构成应用程序并需要一起迁移到 Azure 的计算机的情况下，此功能非常有用。

## <a name="before-you-start"></a>开始之前

- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 如果已创建项目, 请确保已[添加](how-to-assess.md)Azure Migrate:服务器评估工具。
- 请确保已在 Azure Migrate 中发现了计算机;为此, 可以设置适用于[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 设备。 设备将发现本地计算机, 并将元数据和性能数据发送到 Azure Migrate:服务器评估。 [了解详细信息](migrate-appliance.md)。

## <a name="how-does-it-work"></a>工作原理

Azure Migrate 使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖关系可视化。
- 若要利用依赖项可视化功能，需要将现有或新的 Log Analytics 工作区与 Azure Migrate 项目进行关联。
- 只能在创建 Azure Migrate 项目的同一订阅中创建或附加工作区。
- 将 Log Analytics 工作区附加到项目:
    1. 在 "**服务器**" 选项卡**的 Azure Migrate:服务器评估**磁贴, 单击 "**概述**"。
    2. 在 "**概述**" 中, 单击向下箭头以展开 " **Essentials**"。
    3. 在**OMS 工作区**中, 单击 "**需要配置**"。
    4. 在 "**配置工作区**" 中, 指定是否要创建新的工作区或使用现有工作区:
    
    ![添加工作区](./media/how-to-create-group-machine-dependencies/workspace.png)

- 关联一个工作区时，可以选择是创建新的工作区还是附加现有工作区：
  - 创建新工作区时，需要指定工作区的名称。 然后在与迁移项目相同的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的区域内创建工作区。
  - 附加现有的工作区时，可以从迁移项目所在订阅中的所有可用工作区进行选择。 请注意，只有在[服务映射受支持](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)的区域中创建的那些工作区才会列出。 为了能够附加工作区，请确保对该工作区有“读取者”访问权限。

  > [!NOTE]
  > 工作区附加到一个项目后，你将无法再对其进行更改。

- 关联的工作区通过“迁移项目”  键和“项目名称”  值（可用于在 Azure 门户中进行搜索）进行标记。
- 若要导航到与项目关联的工作区，可以转到项目“概述”  页的“Essentials”  部分并访问工作区

    ![导航 Log Analytics 工作区](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用依赖项可视化，你需要在要分析的每台本地计算机上下载并安装代理。  

- 需要在每台计算机上安装 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。 [了解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)有关如何安装 MMA 代理的详细信息。
- 需要在每台计算机上安装[依赖项代理](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。 [了解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)有关如何安装依赖关系代理的详细信息。
- 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 Log Analytics 网关。

除非要使用依赖项可视化，否则在要评估的计算机上不需要安装这些代理。

## <a name="do-i-need-to-pay-for-it"></a>是否需要对其付费？

依赖关系可视化功能免费提供。 使用服务器评估中的依赖项可视化功能需要服务映射, 需要将 Log Analytics 工作区 (新的或现有的) 与 Azure Migrate 项目关联。 服务器评估中的依赖项可视化功能在前180天内免费。

1. 在此 Log Analytics 工作区中使用服务映射以外的任何其他解决方案都会产生[标准 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 费用。
2. 为了不另外收费地支持迁移方案，服务映射解决方案在将 Log Analytics 工作区与 Azure Migrate 项目关联之日起的前 180 天内不会产生任何费用。 在 180 天之后，将收取标准 Log Analytics 费用。

将代理注册到工作区时，请在“安装代理步骤”页上使用项目提供的 ID 和密钥。

删除 Azure Migrate 项目时，工作区不会随之一起删除。 发布项目删除后，使用服务映射不会免费，每个节点将按照 Log Analytics 工作区的付费层进行收费。

> [!NOTE]
> 依赖项可视化功能通过 Log Analytics 工作区使用服务映射。 自 2018 年 2 月 28 日起，随着公告 Azure Migrate 正式发布，现在使用该功能无需额外付费。 你需要创建一个新项目，以便利用免费使用的工作区。 正式发布前的现有工作区仍收费，因此我们建议你迁移到新项目。

[在此处](https://azure.microsoft.com/pricing/details/azure-migrate/)详细了解 Azure Migrate 定价。

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作区？

你可以使用 Azure Migrate 外部的 Log Analytics 工作区。 如果删除创建该项目的 Azure Migrate 项目, 则不会将其删除。 如果你不再需要工作区，请手动[删除工作区](../azure-monitor/platform/manage-access.md)。

请勿删除 Azure Migrate 创建的工作区, 除非删除 Azure Migrate 项目。 否则，依赖项可视化功能将不会按预期工作。

## <a name="next-steps"></a>后续步骤
- [使用计算机依赖项分组计算机](how-to-create-group-machine-dependencies.md)
- [详细了解有关依赖项可视化的常见问题解答](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization)。
