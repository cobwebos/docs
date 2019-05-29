---
title: Durable Functions 概述 - Azure
description: Azure Functions 的 Durable Functions 扩展简介。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 2228f3fe05e1021d0f87ce0b0d33a8287f048a8c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872810"
---
# <a name="what-are-durable-functions"></a>什么是 Durable Functions？

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 的一个扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。

## <a name="benefits"></a>优点

使用此扩展，可以通过[业务流程协调程序函数](durable-functions-types-features-overview.md#orchestrator-functions)定义有状态工作流，这有以下好处： 

* 可在代码中定义工作流。 无需 JSON 架构或设计器。
* 可同步和异步调用其他函数。 调用函数的输出可保存到本地变量。
* 每当有函数处于等待状态时，可自动对进度执行检查点操作。 回收进程或重启 VM 时，从来不会丢失本地状态。

## <a name="application-patterns"></a>应用程序模式

Durable Functions 的主要用例是简化无服务器应用程序中出现的复杂的有状态协调要求。 下面是可受益于 Durable Functions 的部分典型应用程序模式：

* [链接](durable-functions-concepts.md#chaining)
* [扇出/扇入](durable-functions-concepts.md#fan-in-out)
* [异步 HTTP API](durable-functions-concepts.md#async-http)
* [监视](durable-functions-concepts.md#monitoring)
* [人机交互](durable-functions-concepts.md#human)

## <a name="language-support"></a>支持的语言

Durable Functions 目前支持以下语言：

* **C#** ：[预编译的类库](../functions-dotnet-class-library.md)和 [C# 脚本](../functions-reference-csharp.md)。
* **F#** ：预编译的类库和 F# 脚本。 仅 Azure Functions 运行时的版本 1.x 支持 F# 脚本。
* **JavaScript**：仅 Azure Functions 运行时的版本 2.x 支持此语言。 要求使用 1.7.0 版或更高版本的 Durable Functions 扩展。 

Durable Functions 的目标是支持所有 [Azure Functions 语言](../supported-languages.md)。 请参阅 [Durable Functions 问题列表](https://github.com/Azure/azure-functions-durable-extension/issues)，了解支持其他语言所需的最新工作状态。

与 Azure Functions 一样，可以使用 [Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md) 和 [Azure 门户](durable-functions-create-portal.md)通过模板来开发 Durable Functions。

## <a name="billing"></a>计费

Durable Functions 的计费与 Azure Functions 一样。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。

## <a name="jump-right-in"></a>立即投入

可以在不到 10 分钟的时间内开始使用 Durable Functions，只需完成下述某个特定于语言的快速入门教程即可：

* [使用 Visual Studio 2019 的 C#](durable-functions-create-first-csharp.md)
* [使用 Visual Studio Code 的 JavaScript](quickstart-js-vscode.md)

在两个快速入门中，请在本地创建并测试“hello world”持久函数。 然后将函数代码发布到 Azure。 创建的函数将协调对其他函数的调用并将其链接在一起。

## <a name="learn-more"></a>了解详细信息

以下视频重点介绍了 Durable Functions 的优势：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions 是 [Azure Functions](../functions-overview.md) 的高级扩展，因此并不适用于所有应用程序。 若要详细了解 Durable Functions，请参阅 [Durable Functions 模式和技术概念](durable-functions-concepts.md)。 若要与其他 Azure 业务流程技术进行比较，请参阅[比较 Azure Functions 和 Azure 逻辑应用](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Durable Functions 模式和技术概念](durable-functions-concepts.md)
