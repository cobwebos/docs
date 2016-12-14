---
title: "Azure Functions 最佳做法 | Microsoft Docs"
description: "了解 Azure Functions 的最佳做法和模式。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 模式, 最佳做法, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 4544629c47326d448cd99b5d96d79666a56f0274
ms.openlocfilehash: 04a5e190a69b1a1a4d0fe6c49c16ddb15593ba38

---

# <a name="best-practices-for-azure-functions"></a>Azure Functions 最佳做法

##<a name="overview"></a>概述

本文提供实现 Function App 时需考虑的最佳做法集合。 请记住，Azure Function App 是一种 Azure App Service。 因此，这些最佳做法适用。  


## <a name="avoid-large-long-running-functions"></a>避免使用长时间运行的大型函数

长时间运行的大型函数可能会引起意外的超时问题。 由于含有许多 Node.js 依赖项，函数规模可能会很大。 导入这些依赖项会导致加载时间增加，引起意外的超时问题。 Node.js 依赖项可由代码中的多个 `require()` 语句进行显式加载。 基于代码加载的具有其内部依赖项的单个模块，它们也可能是隐式的。  

尽可能将大型函数重构为可协同工作且返回快速响应的较小函数集。 例如，webhook 或 HTTP 触发器函数可能需要在特定时间限制内确认响应。 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 此方法允许延迟实际工作并返回即时响应。 Webhook 通常都需要即时响应。


## <a name="cross-function-communication"></a>跨函数通信。

集成多个函数时，将存储队列用于跨函数通信通常是最佳做法。  主要原因是因为存储队列成本更低、更易预配。 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，则可使用 Azure 服务总线队列，支持最大为 256 KB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。



## <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 例如，如果有必须每天运行一次的内容，则编写它，使它可在一天内的任何时间运行，并生成相同的结果。 某天没有任何工作时，可退出该函数。 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


## <a name="write-defensive-functions"></a>编写防御函数。

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. 在 DB 中进行 10,000 行的查询。
2. 为每行创建队列消息，从而处理下一行。
 
根据系统复杂程度，可能有：行为有误的相关下游服务，网络故障或已达配额限制等等。所有这些可在任何时间影响用户的函数。 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，然后发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 这会严重影响工作流。 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 [Azure 存储队列触发器](functions-bindings-storage.md#storagequeuetrigger)文档中的**处理有害队列消息**。
 



## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一 Function App 中混合测试和生产代码。

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

如果在多个 .Net 函数中引用共享程序集，请将其放在常用的共享文件夹中。 引用具有与以下示例类似语句的程序集： 

    #r "..\Shared\MyAssembly.dll". 

否则，很容易意外部署在函数之间表现不同的同一二进制的多个测试版本。

请勿在生产代码中使用详细日志记录。 其对性能有负面影响。



## <a name="use-async-code-but-avoid-taskresult"></a>使用异步代码，但避免使用 Task.Result

异步编程是推荐的最佳做法。 但请始终避免引用 `Task.Result` 属性。 此方法实质上是对另一线程的锁上执行忙-等待操作。 持有锁可能会导致出现死锁。




## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions C# 开发人员参考](functions-reference-csharp.md)
* [Azure Functions F# 开发人员参考](functions-reference-fsharp.md)
* [Azure Functions NodeJS 开发人员参考](functions-reference-node.md)




<!--HONumber=Nov16_HO3-->


