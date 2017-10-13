---
title: "Azure Functions 最佳做法 | Microsoft Docs"
description: "了解 Azure Functions 的最佳做法和模式。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 模式, 最佳做法, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/13/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e0d4a29f59e48cac675e567fd84384b1b60d35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>优化 Azure Functions 的性能和可靠性

本文为提高函数应用的性能和可靠性提供了指南。 


## <a name="avoid-long-running-functions"></a>避免使用长时间运行的函数

长时间运行的大型函数可能会引起意外超时问题。 函数规模可能因含有许多 Node.js 依赖项而变大。 导入依赖项也会导致加载时间增加，引起意外的超时问题。 显式和隐式加载依赖项。 由代码加载的单个模块可能会加载自己的附加模块。  

尽可能将大型函数重构为可协同工作且快速返回响应的较小函数集。 例如，webhook 或 HTTP 触发器函数可能需要在特定时间限制内确认响应；webhook 需要快速响应，这很常见。 可将 HTTP 触发器有效负载传递到由队列触发器函数处理的队列。 此方法允许延迟实际工作并返回即时响应。


## <a name="cross-function-communication"></a>跨函数通信

集成多个函数时，将存储队列用于跨函数通信通常是最佳做法。  主要原因是因为存储队列成本更低、更易预配。 

存储队列中各消息的大小限制为 64 KB。 如果需要在函数之间传递更大的消息，则可使用 Azure 服务总线队列，支持最大为 256 KB 的消息大小。

如果在处理前需要筛选消息，则服务总线主题十分有用。

对于支持大容量通信，事件中心十分有用。


## <a name="write-functions-to-be-stateless"></a>将函数编写为无状态 

如有可能，函数应为无状态和幂等。 将任何所需的状态信息与用户的数据相关联。 例如，正在处理的排序可能具有关联的 `state` 成员。 函数本身保持无状态时，该函数可根据该状态处理排序。 

对于计时器触发器，特别建议采用幂等函数。 例如，如果有必须每天运行一次的内容，则编写它，使它可在一天内的任何时间运行，并生成相同的结果。 某天没有任何工作时，可退出该函数。 此外，如果未能完成以前的运行，则下次运行应从中断的位置继续运行。


## <a name="write-defensive-functions"></a>编写防御函数

假定任何时候函数都可能会遇到异常。 设计函数，使其具有在下次执行期间从上一失败点继续执行的能力。 请考虑需执行以下操作的方案：

1. 在 DB 中进行 10,000 行的查询。
2. 为每行创建队列消息，从而处理下一行。
 
根据系统复杂程度，可能有：行为有误的相关下游服务，网络故障或已达配额限制等等。所有这些可在任何时间影响用户的函数。 需设计函数，使其做好该准备。

如果将 5,000 个那些项插入到队列中进行处理，然后发生故障，代码将如何响应？ 跟踪已完成的一组中的项。 否则，下次可能再次插入它们。 这会严重影响工作流。 

如果已处理队列项，则允许函数不执行任何操作。

利用已为 Azure Functions 平台中使用的组件提供的防御措施。 有关示例，请参阅 [Azure 存储队列触发器](functions-bindings-storage-queue.md#trigger)文档中的**处理有害队列消息**。
 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>请勿在同一函数应用中混合测试和生产代码

Function App 中的各函数共享资源。 例如，共享内存。 如果生产中使用的是 Function App，则请勿向其添加与测试相关的函数和资源。 生产代码执行期间，这可能会导致意外的开销。

请注意在生产 Function App 中加载的内容。 将内存平均分配给应用中的每个函数。

如果在多个 .Net 函数中引用共享程序集，请将其放在常用的共享文件夹中。 引用具有与以下示例类似语句的程序集： 

    #r "..\Shared\MyAssembly.dll". 

否则，很容易意外部署在函数之间表现不同的同一二进制的多个测试版本。

请勿在生产代码中使用详细日志记录。 其对性能有负面影响。



## <a name="use-async-code-but-avoid-blocking-calls"></a>使用异步代码，但避免阻止调用

异步编程是推荐的最佳做法。 但是，请始终避免引用 `Result` 属性或在 `Task` 实例上调用 `Wait` 方法。 这种方法会导致线程耗尽。


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

由于 Azure Functions 使用 Azure 应用服务，用户还应该了解应用服务准则。
* [模式和实践 HTTP 性能优化](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)

