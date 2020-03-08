---
title: 什么是 Azure 计划程序？
description: 创建在 Azure 内部或外部调用服务的自动作业
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898534"
---
# <a name="what-is-azure-scheduler"></a>什么是 Azure 计划程序？

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)正在替换[正在停](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)用的 azure 计划程序。 若要继续使用在计划程序中设置的作业，请尽快[迁移到 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 计划程序在 Azure 门户中不再可用，但此时[REST API](/rest/api/scheduler)和[Azure 计划程序 PowerShell cmdlet](scheduler-powershell-reference.md)仍可用，以便你可以管理作业和作业集合。

[Azure 计划程序](https://azure.microsoft.com/services/scheduler/)通过以声明性的方式描述操作，帮助创建在云中运行的[作业](../scheduler/scheduler-concepts-terms.md)。 该服务随后自动安排并运行这些操作。 例如，可以在 Azure 内部和外部调用服务（如调用 HTTP 或 HTTPS 终结点），还可以将消息发布到 Azure 存储队列和 Azure 服务总线队列或主题。 可以立即或稍后运行作业。 计划程序轻松支持[复杂计划和高级重复周期](../scheduler/scheduler-advanced-complexity.md)。 计划程序指定何时运行作业、保留可以查看的作业结果历史记录，然后以可预测且可靠的方式安排工作负载运行。

其他 Azure 计划功能也在后台使用计划程序，例如，[Azure WebJobs](../app-service/webjobs-create.md)，这是 Azure 应用服务中的一个 [Web 应用](https://azure.microsoft.com/services/app-service/web/)功能。 您可以使用[计划程序 REST API](https://docs.microsoft.com/rest/api/scheduler/)管理这些操作的通信，这有助于管理这些操作的通信。

计划程序可在以下方案中提供帮助：

* 运行定期应用操作：例如，定期将 Twitter 中的数据收集到源中。

* 执行日常维护：每天删除日志、执行备份和其他维护任务。

  例如，作为管理员，你可能需要在接下来九个月的每天凌晨 1 点备份数据库。

虽然可以使用计划程序来创建、维护和运行安排的工作负载，但计划程序不会托管工作负载或运行代码。 该服务仅*调用*在其他位置托管的服务或代码，例如，在 Azure 中或本地托管，或与其他提供程序一起托管。 计划程序可以通过 HTTP、HTTPS、存储队列、服务总线队列或服务总线主题进行调用。

若要创建、计划、管理、更新或删除作业和[作业集合](../scheduler/scheduler-concepts-terms.md)，可以使用代码、[计划程序 REST API](https://docs.microsoft.com/rest/api/scheduler/)或[Azure 计划程序 PowerShell cmdlet](scheduler-powershell-reference.md)。

## <a name="next-steps"></a>后续步骤

* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的计划和计费](scheduler-plans-billing.md)
* [利用 Azure 计划程序生成复杂的计划和高级重复周期](scheduler-advanced-complexity.md)
* [Azure 计划程序 REST API 参考](/rest/api/scheduler)
* [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)
