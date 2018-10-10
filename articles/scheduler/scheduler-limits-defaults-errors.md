---
title: Azure 计划程序中的限制、配额和阈值
description: 了解 Azure 计划程序的限制、配额、默认值和限制阈值
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 0c1e704a3bdec239c87d879ae1ef95e6e76d27fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966876"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure 计划程序中的限制、配额和限制阈值

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换即将停用的 Azure 计划程序。 若要安排作业，请[改为试用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

## <a name="limits-quotas-and-thresholds"></a>限制、配额和阈值

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 标头

对计划程序服务发出的每个请求都会返回一个名为 **x-ms-request-id** 的响应标头。此响应标头包含一个唯一标识此请求的不透明值。 因此，如果某个请求始终失败，而你确认该请求的格式正确，则可以通过提供 **x-ms-request-id** 响应标头值并包含以下详细信息来向 Microsoft 报告错误： 

* **x-ms-request-id** 值
* 发出请求的大致时间 
* Azure 订阅、作业集合和作业的标识符 
* 请求尝试的操作类型

## <a name="see-also"></a>另请参阅

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
