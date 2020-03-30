---
title: 使用 Azure 函数计时器按计划缩放 Azure 宇宙数据库
description: 了解如何使用 PowerShell 和 Azure 函数缩放 Azure Cosmos DB 中的吞吐量更改。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935163"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>使用 Azure 函数计时器触发器缩放 Azure 宇宙数据库吞吐量

Azure Cosmos 帐户的性能基于以每秒请求单位 （RU/s） 表示的预配吞吐量量。 预配是第二粒度，并且根据每小时的最高 RU/s 计费。 这种预配的容量模型使服务能够提供可预测且一致的吞吐量，保证低延迟、高可用性。 大多数生产工作负载都具有这些功能。 但是，在 Azure Cosmos DB 仅在工作时间使用的开发和测试环境中，可以提高上午的吞吐量，并在工作时间后在晚上缩减。

您可以通过[Azure 资源管理器模板](resource-manager-samples.md)[、Azure CLI](cli-samples.md)和[PowerShell](powershell-samples-sql.md)为核心 （SQL） API 帐户，或使用特定于语言的 Azure Cosmos DB SDK 设置吞吐量。 使用资源管理器模板、Azure CLI 或 PowerShell 的好处是它们支持所有 Azure Cosmos DB 模型 API。

## <a name="throughput-scheduler-sample-project"></a>吞吐量计划程序示例项目

为了简化按计划缩放 Azure Cosmos DB 的过程，我们创建了一个示例项目，称为[Azure Cosmos 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)。 此项目是具有两个计时器触发器的 Azure 函数应用 - "缩放向上触发器"和"缩放向下触发器"。 触发器运行 PowerShell 脚本，该脚本按每个触发器中`resources.json`文件中定义的每个资源设置吞吐量。 ScaleUpTrigger 配置为在 UTC 上午 8 点运行，缩放向下触发器配置为在 UTC 下午 6 点运行，这些时间可以轻松地`function.json`在每个触发器的文件内更新。

您可以在本地克隆此项目，对其进行修改以指定要向上和向下缩放的 Azure Cosmos DB 资源以及要运行的计划。 稍后，您可以在 Azure 订阅中部署它，并使用托管服务标识使用[基于角色的访问控制](role-based-access-control.md)（RBAC） 权限与"Azure Cosmos DB 运算符"角色进行保护，以在 Azure Cosmos 帐户上设置吞吐量。

## <a name="next-steps"></a>后续步骤

- 了解更多信息并从[Azure Cosmos DB 吞吐量计划程序](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)下载示例。
