---
title: Azure 计划程序中的限制、配额和阈值
description: 了解 Azure 计划程序的限制、配额、默认值和限制阈值
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898529"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure 计划程序中的限制、配额和限制阈值

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)正在替换[正在停](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)用的 azure 计划程序。 若要继续使用在计划程序中设置的作业，请尽快[迁移到 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 计划程序在 Azure 门户中不再可用，但此时[REST API](/rest/api/scheduler)和[Azure 计划程序 PowerShell cmdlet](scheduler-powershell-reference.md)仍可用，以便你可以管理作业和作业集合。

## <a name="limits-quotas-and-thresholds"></a>限制、配额和阈值

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 标头

对计划程序服务发出的每个请求将返回名为**x-ms 的**响应标头。此标头包含唯一标识此请求的不透明值。 因此，如果某个请求始终失败，而你确认该请求的格式正确，则可以通过提供 **x-ms-request-id** 响应标头值并包含以下详细信息来向 Microsoft 报告错误： 

* **x-ms-request-id** 值
* 发出请求的大致时间 
* Azure 订阅、作业集合和作业的标识符 
* 请求尝试的操作类型

## <a name="next-steps"></a>后续步骤

* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的计划和计费](scheduler-plans-billing.md)
* [Azure 计划程序 REST API 参考](/rest/api/scheduler)
* [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)