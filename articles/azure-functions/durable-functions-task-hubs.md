---
title: Durable Functions 中的任务中心 - Azure
description: 了解在 Azure Functions 的 Durable Functions 扩展中什么是任务中心。 了解如何配置任务中心。
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions 中的任务中心 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 中的*任务中心*是用于业务流程的 Azure 存储资源的逻辑容器。 只有当业务流程协调程序函数与活动函数属于同一任务中心时，它们才能彼此进行交互。

每个函数应用都有一个单独的任务中心。 如果多个函数应用共享一个存储帐户，则该存储帐户包含多个任务中心。 下图说明了在共享和专用存储帐户中每个函数应用有一个任务中心。

![说明共享和专用存储帐户的关系图。](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure 存储资源

任务中心包含以下存储资源： 

* 一个或多个控制队列。
* 一个工作项队列。
* 一个历史记录表。
* 一个实例表。
* 一个包含一个或多个租用 blob 的存储容器。

当业务流程协调程序或活动函数运行时或调度它们运行时，将自动在默认 Azure 存储帐户中创建所有这些资源。 [性能和缩放](durable-functions-perf-and-scale.md)一文介绍了如何使用这些资源。

## <a name="task-hub-names"></a>任务中心名称

任务中心由 *host.json* 文件中声明的名称标识，如以下示例所示：

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，默认名称是 **DurableFunctionsHub**。

> [!NOTE]
> 当共享存储帐户中有多个任务中心时，名称用于将一个任务中心与其他任务中心区分开来。 如果有多个函数应用共享一个共享存储帐户，则必须在 *host.json* 文件中为每个任务中心配置不同的名称。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)
