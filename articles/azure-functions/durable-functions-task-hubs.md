---
title: "Durable Functions 中的任务中心 - Azure"
description: "了解在 Azure Functions 的 Durable Functions 扩展中什么是任务中心。 了解如何配置任务中心。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions 中的任务中心 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 的“任务中心”是单个 Azure 存储帐户的上下文中的业务流程和活动的逻辑容器。 多个函数甚至函数应用可以存在于同一任务中心内，并且任务中心通常用作应用程序容器。

不需要显式创建任务中心。 它们是由运行时自动初始化的，并使用在 *host.json* 文件中声明的名称。 每个任务中心都在单个存储帐户内有其自己的一组存储队列、表和 blob。 在给定任务中心内运行的所有函数应用都共享相同的存储资源。 只有当业务流程协调程序函数与活动函数属于同一任务中心时，它们才能彼此进行交互。

## <a name="configuring-a-task-hub-in-hostjson"></a>在 host.json 中配置任务中心

可以在函数应用的 *host.json* 文件中配置任务中心名称。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则函数应用的默认任务中心名称是 **DurableFunctionsHub**。

> [!NOTE]
> 如果有多个函数应用共享同一个存储帐户，建议为每个函数应用配置一个不同的任务中心名称。 这可以确保每个函数应用正确地彼此隔离。

## <a name="azure-storage-resources"></a>Azure 存储资源

任务中心包含多个 Azure 存储资源：

* 一个或多个控制队列。
* 一个工作项队列。
* 一个历史记录表。
* 一个包含一个或多个租用 blob 的存储容器。

当业务流程协调程序或活动函数运行时或调度它们运行时，将自动在默认 Azure 存储帐户中创建所有这些资源。 [性能和缩放](durable-functions-perf-and-scale.md)一文介绍了如何使用这些资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)

