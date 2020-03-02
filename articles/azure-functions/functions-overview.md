---
title: Azure Functions 概述
description: 了解如何使用 Azure Functions 以分钟为单位优化异步工作负荷。
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621007"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 简介

Azure Functions 允许你运行小段代码（称为“函数”）且不需要担心应用程序基础结构。 借助 Azure Functions，云基础结构可以提供应用程序保持规模化运行所需的所有最新状态的服务器。

函数由特定类型的事件“触发”。 [支持的触发器](./functions-triggers-bindings.md)包括对数据更改做出响应、对消息做出响应、按计划运行，或者生成 HTTP 请求的结果。

虽然你始终可以直接针对大量服务编写代码，但使用绑定可以简化与其他服务的集成。 使用绑定，你能够[以声明方式访问各种 Azure 服务和第三方服务](./functions-triggers-bindings.md)。

## <a name="features"></a>功能

Azure Functions 的一些主要功能包括：

- **无服务器应用程序**：使用 Functions，可在 Microsoft Azure 上开发[无服务器](https://azure.microsoft.com/solutions/serverless/)应用程序。

- **语言选择**：使用所选的 [C#、Java、JavaScript、Python 和 PowerShell](supported-languages.md) 编写函数。

- **按使用付费定价模型**：仅为运行代码所用的时间付费。 请参阅[定价部分](#pricing)中的使用托管计划选项。  

- **自带依赖项**：Functions 支持 NuGet 和 NPM，允许你访问你喜欢的库。

- **集成的安全性**：使用 OAuth 提供程序（如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帐户）保护 HTTP 触发的函数。

- **简化的集成**：轻松与 Azure 服务和软件即服务 (SaaS) 产品/服务进行集成。

- **灵活开发**：直接在门户中编写函数代码，或者通过 [GitHub](../app-service/scripts/cli-continuous-deployment-github.md)、[Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) 和其他[受支持的开发工具](../app-service/deploy-local-git.md)设置持续集成和部署代码。

- **有状态无服务器体系结构**：使用 [Durable Functions](durable/durable-functions-overview.md) 协调无服务器应用程序。

- **开放源代码**：Functions 运行时是开源的，[可在 GitHub 上找到](https://github.com/azure/azure-webjobs-sdk-script)。

## <a name="what-can-i-do-with-functions"></a>使用 Functions 可以做什么？

Functions 是一个理想的解决方案，用于处理批量数据、集成系统、使用物联网 (IoT) 以及生成简单的 API 和微服务。

有一系列模板可帮助你开始使用关键方案，包括：

- **HTTP**：基于 [HTTP 请求](functions-create-first-azure-function.md)运行代码

- **计时器**：将代码安排[在预定义的时间运行](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**：处理[新的和修改的 Azure Cosmos DB 文档](./functions-create-cosmos-db-triggered-function.md)

- **Blob 存储**：处理[新的和修改的 Azure 存储 blob](./functions-create-storage-blob-triggered-function.md)

- **队列存储**：响应 [Azure 存储队列消息](./functions-create-storage-queue-triggered-function.md)

- **事件网格**：[通过订阅和筛选器响应 Azure 事件网格事件](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **事件中心**：响应[大量 Azure 事件中心事件](./functions-bindings-event-hubs.md)

- **服务总线队列**：通过[对服务总线队列消息做出响应](./functions-bindings-service-bus.md)连接到其他 Azure 服务或本地服务

- **服务总线主题**：通过[对服务总线主题消息做出响应](./functions-bindings-service-bus.md)连接到其他 Azure 服务或本地服务

## <a name="pricing"></a>Functions 的费用是多少？

Azure Functions 有三种定价计划。 请选择最适合自己的那种：

- **消耗计划**：Azure 提供了所有必要的计算资源。 你不必担心资源管理，只需为你的代码运行的时间付费。

- **高级计划**：你指定多个预热实例，这些实例始终处于联机状态，并随时可以立即响应。 函数运行时，Azure 会提供所需的任何其他计算资源。 你需要为持续运行的预热实例以及在 Azure 缩放应用时使用的任何其他实例付费。

- **应用服务计划**：将函数像 Web 应用一样运行。 如果已对其他应用程序使用应用服务，可以按相同的计划运行你的函数，不用另外付费。

有关托管计划的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。 完整的定价详细信息可在 [Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)中找到。

## <a name="next-steps"></a>后续步骤

- [创建第一个 Azure 函数](functions-create-first-function-vs-code.md)  
  通过 [Visual Studio Code](functions-create-first-function-vs-code.md)、[命令行](functions-create-first-azure-function-azure-cli.md)开始入门，也可以使用 [Azure 门户](functions-create-first-azure-function.md)。

- [Azure Functions 开发人员参考](functions-reference.md)  
  提供有关 Azure Functions 运行时的更多技术信息，并为编码函数及定义触发器和绑定提供参考。

- [如何缩放 Azure Functions](functions-scale.md)  
  讨论 Azure Functions 提供的服务计划（包括使用托管计划）以及如何选择合适的计划。

- [详细了解 Azure 应用服务](../app-service/overview.md)  
  Azure Functions 利用 Azure 应用服务执行核心功能，例如部署、环境变量和诊断。
