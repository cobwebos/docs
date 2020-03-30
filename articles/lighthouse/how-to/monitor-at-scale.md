---
title: 大规模监视委派资源
description: 了解如何跨要管理的客户租户以可扩展的方式有效地使用 Azure 监视器日志。
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122891"
---
# <a name="monitor-delegated-resources-at-scale"></a>大规模监视委派资源

作为服务提供商，你可能已经载入了多个客户租户进行 Azure 委派资源管理。 [Azure Lighthouse](../overview.md) 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题介绍如何在所管理的客户租户之间以可扩展的方式使用[Azure 监视器日志](../../azure-monitor/platform/data-platform-logs.md)。

## <a name="create-log-analytics-workspaces"></a>创建日志分析工作区

为了收集数据，您需要创建日志分析工作区。 这些日志分析工作区是 Azure 监视器收集的数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。

我们建议直接在客户租户中创建这些工作区。 这样，他们的数据将保留在租户中，而不是导出到您的租户中。 这还允许集中监视 Log Analytics 支持的任何资源或服务，使您能够在监视哪些类型的数据方面拥有更大的灵活性。

可以使用[Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)或使用[Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)或使用[Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)来创建日志分析工作区。

## <a name="deploy-policies-that-log-data"></a>部署记录数据的策略

创建日志分析工作区后，可以跨客户层次结构部署 Azure[策略](../../governance/policy/index.yml)，以便将诊断数据发送到每个租户中的相应工作区。 您部署的确切策略可能因要监视的资源类型而异。

要了解有关创建策略的更多信息，请参阅[教程：创建和管理策略以强制实施合规性](../../governance/policy/tutorials/create-and-manage.md)。 此[社区工具](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator)提供一个脚本，可帮助您创建策略来监视您选择的特定资源类型。

确定要部署哪些策略后，可以[大规模将它们部署到委派的订阅](policy-at-scale.md)。

## <a name="analyze-the-gathered-data"></a>分析收集的数据

部署策略后，数据将记录在每个客户租户中创建的日志分析工作区中。 要跨所有托管客户获取见解，可以使用[Azure 监视器工作簿](../../azure-monitor/platform/workbooks-overview.md)等工具收集和分析来自多个数据源的信息。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure Monitor](../../azure-monitor/index.yml) 的信息。
- 了解[Azure 监视器日志](../../azure-monitor/platform/data-platform-logs.md)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
