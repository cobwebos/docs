---
title: Azure Migrate 中的依赖项可视化
description: 概述中的服务器评估服务中的评估计算 Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425674"
---
# <a name="dependency-visualization"></a>依赖项可视化

本文介绍 Azure Migrate：服务器评估中的依赖关系可视化功能。

依赖关系可视化有助于了解要评估和迁移的计算机之间的依赖关系。 当您想要评估具有更高置信度的计算机时，通常使用依赖关系映射。

- 在 Azure Migrate：服务器评估中，将计算机组合到一起进行评估。 组通常由要一起迁移的计算机组成，依赖关系可视化可帮助您交叉检查计算机依赖项，以便您可以准确地对计算机进行分组。
- 使用可视化效果，可以发现需要一起迁移的相互依赖的系统。 您可以确定正在使用的系统是仍在使用中，还是可以取消使用（而不是迁移）系统。
- 可视化依赖项有助于确保不会遗留任何内容，并避免在迁移过程中出现意外中断。
- 如果你未完全了解作为应用一部分的计算机，并且应将其一起迁移到 Azure，则此功能特别有用。


> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="agent-based-and-agentless"></a>基于代理和无代理

可通过两个选项来部署依赖关系可视化：

- **无代理依赖项可视化**：此选项当前为预览版，仅适用于 VMware vm。 不需要在计算机上安装任何代理。 
    - 它的工作原理是从启用了它的计算机捕获 TCP 连接数据。 [了解详细信息](how-to-create-group-machine-dependencies-agentless.md)。
启动依赖项发现后，设备会按5分钟的轮询间隔从计算机收集数据。
    - 收集以下数据：
        - TCP 连接
        - 具有活动连接的进程的名称
        - 运行上述进程的已安装应用程序的名称
        - No。 每个轮询间隔检测到的连接
- **基于代理的依赖项可视化**：若要使用基于代理的依赖项可视化，需要在要分析的每台本地计算机上下载并安装以下代理。  
    - 需要在每台计算机上安装 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。 [了解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)有关如何安装 MMA 代理的详细信息。
    - 需要在每台计算机上安装[依赖项代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。 [了解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)有关如何安装依赖关系代理的详细信息。
    - 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 Log Analytics 网关。

## <a name="agent-based-requirements"></a>基于代理的要求

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>部署依赖关系可视化需要执行哪些操作？

在部署依赖项可视化之前，应准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。 将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。

[详细了解](how-to-assess.md)如何添加该工具，以及如何为[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或物理服务器部署设备。


### <a name="how-does-it-work"></a>它的工作原理是怎样的？

Azure Migrate 使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖关系可视化。

- 若要利用依赖项可视化，需要将 Log Analytics 工作区（新的或现有的）与 Azure Migrate 的项目相关联。
- 工作区必须与在其中创建 Azure Migrate 项目的订阅相同。
- Azure Migrate 支持位于美国东部、东南亚和西欧区域的工作区。 其他区域中的工作区不能与项目关联。 另请注意，工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。
- 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
- 在 Log Analytics 中，与 Azure Migrate 相关联的工作区用迁移项目密钥和项目名称进行标记。

    ![导航 Log Analytics 工作区](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>是否需要对其付费？

依赖项可视化需要服务映射和关联的 Log Analytics 工作区。 

- 服务映射解决方案在前180天不会产生任何费用。 这是从你将 Log Analytics 工作区与 Azure Migrate 项目关联的那一天。
- 在 180 天之后，将收取标准 Log Analytics 费用。
- 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将产生[标准 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)费用。
- 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用情况并不免费，每个节点将按 Log Analytics 工作区的付费层收费。

[在此处](https://azure.microsoft.com/pricing/details/azure-migrate/)详细了解 Azure Migrate 定价。

> [!NOTE]
> 如果你的项目是在2018年2月28日 Azure Migrate 公开上市之前创建的，则可能会产生额外服务映射费用。 为了确保你只需在180天后支付费用，我们建议你创建一个新项目，因为在正式发布前现有工作区仍可计费。



### <a name="how-do-i-manage-the-workspace"></a>如何管理工作区？

- 将代理注册到工作区时，将使用 Azure Migrate 项目提供的 ID 和密钥。
- 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。
- 如果删除关联的 Azure Migrate 项目，则不会自动删除该工作区。 需要手动将[其删除](../azure-monitor/platform/manage-access.md)。
- 请勿删除 Azure Migrate 创建的工作区，除非删除 Azure Migrate 项目。 否则，依赖项可视化功能将不会按预期工作。

## <a name="next-steps"></a>后续步骤
- [使用计算机依赖项分组计算机](how-to-create-group-machine-dependencies.md)
- [详细了解有关依赖项可视化的常见问题解答](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


