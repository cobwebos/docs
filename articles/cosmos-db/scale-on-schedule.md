---
title: 使用 Azure Functions 计时器按计划缩放 Azure Cosmos DB
description: 了解如何使用 PowerShell 和 Azure Functions 在 Azure Cosmos DB 中缩放吞吐量更改。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935163"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>使用 Azure Functions 计时器触发器缩放 Azure Cosmos DB 吞吐量

Azure Cosmos 帐户的性能以每秒请求单位数（RU/s）表示的预配吞吐量的数量为基础。 预配的粒度为第二个，根据每小时的最大 RU/秒进行计费。 这种预配的容量模型使服务能够提供可预测且一致的吞吐量，保证低延迟、高可用性。 大多数生产工作负荷都提供这些功能。 但是，在开发和测试环境中，仅在工作时间内使用 Azure Cosmos DB 时，你可以在早上增加吞吐量，并在工作时间后晚上向下缩放。

可以通过[Azure 资源管理器模板](resource-manager-samples.md)、 [Azure CLI](cli-samples.md)和[PowerShell](powershell-samples-sql.md)设置吞吐量，对于核心（SQL） API 帐户，或使用特定于语言的 Azure Cosmos DB sdk。 使用资源管理器模板 Azure CLI 或 PowerShell 的好处在于它们支持所有 Azure Cosmos DB 模型 Api。

## <a name="throughput-scheduler-sample-project"></a>吞吐量计划程序示例项目

为了简化按计划缩放 Azure Cosmos DB 的过程，我们创建了一个名为 " [Azure Cosmos 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)" 的示例项目。 此项目是一个具有两个计时器触发器的 Azure Functions 应用-"ScaleUpTrigger" 和 "ScaleDownTrigger"。 触发器运行 PowerShell 脚本，该脚本设置每个触发器的 `resources.json` 文件中定义的每个资源的吞吐量。 ScaleUpTrigger 配置为在上午8点 UTC 运行，ScaleDownTrigger 配置为在下午6点 UTC 运行，并且在每个触发器的 `function.json` 文件中可以轻松地更新这些时间。

你可以在本地克隆此项目，对其进行修改，以指定要增加和减少的 Azure Cosmos DB 资源以及要运行的计划。 稍后，你可以将其部署到 Azure 订阅中，并使用托管服务标识（具有 "Azure Cosmos DB 操作员" 角色的[基于角色的访问控制](role-based-access-control.md)（RBAC）权限）对其进行保护，以便在 Azure Cosmos 帐户上设置吞吐量。

## <a name="next-steps"></a>后续步骤

- 了解详细信息，并从[Azure Cosmos DB 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)下载该示例。
