---
title: "Azure Migrate 中的依赖项可视化 | Microsoft 文档"
description: "概述 Azure Migrate 服务中的评估计算。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: 886977764517f1fec89eee77fc3263d30ff9ab31
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="dependency-visualization"></a>依赖项可视化

[Azure Migrate](migrate-overview.md) 服务会评估要迁移到 Azure 的本地计算机组。 可以使用依赖项可视化将计算机组合在一起。 本文提供了有关此功能的信息。


## <a name="overview"></a>概述

通过 Azure Migrate 中的依赖项可视化，你可以更有把握地创建迁移评估的组。 使用依赖项可视化，你可以查看特定计算机或一组计算机内的网络依赖项。 这将有助于在应用和工作负载跨多台计算机运行时，避免在迁移过程中出现功能不起作用或丢失（或计算机被遗忘）的情况。  

## <a name="how-does-it-work"></a>工作原理

Azure Migrate 使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案来实现依赖项可视化。
- 创建 Azure 迁移项目时，会在你的订阅中创建一个 OMS Log Analytics 工作区。
- 工作区名称是你为迁移项目指定的名称，以 **migrate-** 为前缀，并且（可选）以数字为后缀。 
- 从项目“概述”页的“概要”部分导航到 Log Analytics 工作区。
- 创建的工作区使用密钥 **MigrateProject** 和值“项目名称”进行标记。 你可以使用这些信息在 Azure 门户中进行搜索。  

    ![Log Analytics 工作区](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用依赖项可视化，你需要在要分析的每台本地计算机上下载并安装代理。  

## <a name="do-i-need-to-pay-for-it"></a>是否需要对其付费？

[在此处](https://azure.microsoft.com/pricing/details/azure-migrate/)详细了解 Azure Migrate 定价。 

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作区？

你可以使用 Azure Migrate 外部的 Log Analytics 工作区。 如果删除在其中创建工作区的迁移项目，不会删除工作区。 如果你不再需要工作区，请手动[删除工作区](../log-analytics/log-analytics-manage-access.md)。

不要删除由 Azure Migrate 创建的工作区，除非你删除迁移项目。 如果删除，则依赖项将无法按预期方式工作。

## <a name="next-steps"></a>后续步骤

[使用计算机依赖项分组计算机](how-to-create-group-machine-dependencies.md)