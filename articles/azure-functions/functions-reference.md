---
title: Azure Functions 开发指南 | Microsoft 文档
description: 了解在 Azure 中开发函数时需要掌握的 Azure Functions 概念和技术，包括各种编程语言和绑定。
author: ggailey777
manager: gwallace
keywords: 开发人员指南, Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: c60fedfe855cc803ee2f4b1c463e2b0614239c04
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982634"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions 开发人员指南
在 Azure Functions 中，特定函数共享一些核心技术概念和组件，不受所用语言或绑定限制。 跳转学习某个特定语言或绑定的详细信息之前，请务必通读此通用概述。

本文假定你已阅读 [Azure Functions 概述](functions-overview.md)。

## <a name="function-code"></a>函数代码
函数是 Azure Functions 的基本概念。 函数包含两个重要部分，即可以用各种语言编写的代码，以及一些配置，function.json 文件。 对于编译语言，此配置文件是从代码中的注释自动生成的。 对于脚本语言，必须自己提供配置文件。

Function.json 文件定义函数触发器、绑定和其他配置设置。 每个函数有且只有一个触发器。 运行时使用此配置文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 下面是一个示例 function.json 文件。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

有关详细信息，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

在 `bindings` 属性配置两个触发器和绑定。 每个绑定共享一些通用设置和一些特定于个别类型的绑定的设置。 每个绑定都需要以下设置：

| 属性 | 值/类型 | 注释 |
| --- | --- | --- |
| `type` |string |绑定类型。 例如， `queueTrigger` 。 |
| `direction` |'in', 'out' |表示绑定是用于接收数据到函数中或是从函数发送数据。 |
| `name` |string |将用于函数中绑定数据的名称。 对于 C#，它将是参数名称；对于 JavaScript，它是键/值列表中的键。 |

## <a name="function-app"></a>Function App
函数应用在 Azure 中提供用于运行函数的执行上下文。 因此, 它是功能的部署和管理单元。 函数应用由一个或多个共同管理、部署和缩放的独立函数组成。 函数应用中的所有函数共享相同的定价计划、部署方法和运行时版本。 将函数应用视为组织和共同管理函数的一种方法。 若要了解详细信息, 请参阅[如何管理 function app](functions-how-to-use-azure-function-app-settings.md)。 

> [!NOTE]
> 函数应用中的所有函数必须使用相同的语言编写。 在 Azure Functions 运行时的[先前版本](functions-versions.md)中，这不是必需的。

## <a name="folder-structure"></a>文件夹结构
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

以上是 Function app 的默认（和推荐）文件夹结构。 如果要更改函数代码的文件位置，请修改 function.json 文件的 `scriptFile` 部分。 我们还建议使用[包部署](deployment-zip-push.md)将项目部署到 Azure 中的函数应用。 也可以使用现有工具，比如[持续集成和部署](functions-continuous-deployment.md)以及 Azure DevOps。

> [!NOTE]
> 如果手动部署包，请确保将 host.json 文件和函数文件夹直接部署到 `wwwroot` 文件夹。 请勿在部署中包含 `wwwroot` 文件夹。 否则，最后将得到 `wwwroot\wwwroot` 文件夹。

#### <a name="use-local-tools-and-publishing"></a>使用本地工具和发布
可以使用各种工具创作和发布各种函数应用，包括 [Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](functions-create-first-function-vs-code.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md) 和 [Azure Functions Core Tools](./functions-develop-local.md)。 有关详细信息，请参阅[在本地对 Azure Functions 进行编码和测试](./functions-develop-local.md)。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> 如何编辑 Azure 门户中的函数
通过 Azure 门户中内置的函数编辑器可直接内联更新代码和 function.json 文件。 建议仅用于小的更改或概念证明 - 最佳做法是使用 VS Code 等本地开发工具。

## <a name="parallel-execution"></a>并行执行
多个触发事件发生的速度超过了单线程函数运行的处理速度时，运行时可并行多次调用函数。  如果 Function App 正在使用[消耗量托管计划](functions-scale.md#how-the-consumption-and-premium-plans-work)，则 Function App 可自动扩大。  无论应用是在消耗量托管计划还是常规[应用服务托管计划](../app-service/overview-hosting-plans.md)上运行，每个 Function App 实例都可能使用多个线程并行处理并发函数调用。  每个 Function App 实例中并发函数的最大调用数根据所用触发器类型以及 Function App 中其他函数所用资源而有所不同。

## <a name="functions-runtime-versioning"></a>Functions 运行时版本控制

可使用 `FUNCTIONS_EXTENSION_VERSION` 应用设置配置 Functions 运行时的版本。 例如，值“~2”表示 Function App 将使用 2.x 作为其主版本。 Function Apps 在发布后，将升级到各自新的次要版本。 有关详细信息（包括如何查看函数应用的确切版本），请参阅[如何针对 Azure Functions 运行时版本](set-runtime-version.md)。

## <a name="repositories"></a>存储库
Azure Functions 代码为开放源，位于 GitHub 存储库：

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 主机](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 门户](https://github.com/azure/azure-functions-ux)
* [Azure Functions 模板](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 扩展](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>绑定
下面是所有受支持的绑定表。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

对来自绑定的错误怀有疑问？ 请查看 [Azure Functions 绑定错误代码](functions-bindings-error-pages.md)文档。

## <a name="reporting-issues"></a>报告问题
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* [在本地对 Azure Functions 进行编码和测试](./functions-develop-local.md)
* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)
* [Azure Functions NodeJS 开发人员参考](functions-reference-node.md)
