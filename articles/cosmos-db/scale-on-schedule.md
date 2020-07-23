---
title: 使用 Azure Functions 计时器按计划缩放 Azure Cosmos DB
description: 了解如何使用 PowerShell 和 Azure Functions 在 Azure Cosmos DB 中缩放吞吐量更改。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 9f538b02e81d885e22a6417d7c1f139c22635b0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262474"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>使用 Azure Functions 计时器触发器缩放 Azure Cosmos DB 吞吐量

Azure Cosmos 帐户的性能基于以每秒请求单位数（RU/秒）表示的预配吞吐量的数量。 预配粒度为秒，并且基于最高的 RU/秒按小时计费。 这种预配的容量模型使服务能够提供可预测且一致的吞吐量，保证低延迟、高可用性。 大多数生产工作负荷都具有这些特征。 但是，在开发和测试环境中，如果 Azure Cosmos DB 只在工作时间使用，则可以在上午提高吞吐量，在晚上在工作时间过后降低吞吐量。

可以通过 [Azure 资源管理器模板](resource-manager-samples.md)、[Azure CLI](cli-samples.md) 和 [PowerShell](powershell-samples-sql.md) 为 Core (SQL) API 帐户设置吞吐量，或者使用语言特定的 Azure Cosmos DB SDK。 使用资源管理器模板、Azure CLI 或 PowerShell 的好处在于它们支持所有 Azure Cosmos DB 模型 API。

## <a name="throughput-scheduler-sample-project"></a>吞吐量计划程序示例项目

为了简化按计划缩放 Azure Cosmos DB 的过程，我们创建了一个名为 [Azure Cosmos 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)的示例项目。 此项目是一个 Azure Functions 应用，它有两个计时器触发器：ScaleUpTrigger 和 ScaleDownTrigger。 触发器运行一个 PowerShell 脚本，该脚本根据每个触发器的 `resources.json` 文件中的定义在每个资源上设置吞吐量。 ScaleUpTrigger 配置为在 UTC 上午 8 点运行，ScaleDownTrigger 配置为在 UTC 下午 6 点运行，可以在每个触发器的 `function.json` 文件内轻松更新这些时间。

你可以在本地克隆此项目，对其进行修改，以指定要增加和减少的 Azure Cosmos DB 资源以及要运行的计划。 稍后，你可以将其部署到 Azure 订阅中，并使用托管服务标识以及[基于角色的访问控制](role-based-access-control.md) (RBAC) 权限对其进行保护（使用“Azure Cosmos DB 操作员”角色），以在 Azure Cosmos 帐户上设置吞吐量。

## <a name="next-steps"></a>后续步骤

- 详细了解并下载 [Azure Cosmos DB 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)中的示例。
