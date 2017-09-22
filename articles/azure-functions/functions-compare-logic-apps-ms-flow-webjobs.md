---
title: "在流、逻辑应用、函数和 Web 作业之间进行选择 | Microsoft Docs"
description: "比较和对比来自 Microsoft 的 4 个云集成服务，并决定应使用哪个服务。"
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow、流、逻辑应用、azure functions、函数、azure webjobs、webjobs，事件处理、动态计算、无服务器体系结构"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/07/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: d8edabe1198f37513fc292e0bffc5c83e7e566e7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/08/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>在流、逻辑应用、函数和 WebJobs 之间进行选择
本文对 Microsoft 云中的以下服务进行比较和对比，这些服务都可以解决集成问题并自动化业务流程：

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure 应用服务 WebJobs](../app-service-web/web-sites-create-web-jobs.md)

将分散的系统"粘附"在一起时，所有这些服务很有用。 它们都可以定义输入、操作、条件和输出。 可以在日程安排或触发器中运行其中一个。 但是，每个服务都有其独特的优点，进行比较并不能解决“哪个服务最佳？”的问题， 而是“哪个服务最适合于这种情况？”问题。 通常，这些服务的组合是快速构建可扩展、全功能的集成解决方案的最佳方法。

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow vs.逻辑应用
Microsoft Flow 和 Azure 逻辑应用可以一起讨论，这是因为它们都是以配置为首的集成服务。 通过它们可轻松构建流程和工作流，并与各种 SaaS 和企业应用程序集成。 

* 流构建在逻辑应用之上
* 它们具有相同的工作流设计器
* [连接器](../connectors/apis-list.md)工作，便也可在另一个中工作

借助流，任何办公室工作人员都可以执行简单的集成（例如，获取重要电子邮件的短信），而无需通过开发人员或 IT。 另一方面，逻辑应用可启用需要企业级 DevOps 和安全实践的高级或任务关键型集成（例如 B2B 进程）。 对于业务工作流，其典型特征就是复杂性会随时间增长而增加。 相应地，可以先从流开始，然后根据需要将其转换到逻辑应用。

下表有助于确定流或逻辑应用是否最适合给定的集成。

|  | 流向 | Logic Apps |
| --- | --- | --- |
| 目标受众 |办公室工作人员、业务用户 |IT 专业人士、开发人员 |
| 方案 |自助服务 |关键任务 |
| 设计工具 |浏览器内和移动应用、仅 UI |浏览器和 [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)、[代码视图](../logic-apps/logic-apps-author-definitions.md)可用 |
| DevOps |临时，在生产中开发 |[Azure 资源管理](../logic-apps/logic-apps-arm-provision.md)中的源控件、测试、支持及自动化和可管理性 |
| 管理员体验 |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| “安全” |标准做法：对敏感数据的[数据主权](https://wikipedia.org/wiki/Technological_Sovereignty)、[静态加密](https://wikipedia.org/wiki/Data_at_rest#Encryption)等。 |Azure 的安全保证：[Azure 安全性](https://www.microsoft.com/trustcenter/Security/AzureSecurity)、[安全中心](https://azure.microsoft.com/services/security-center/)、[审核日志](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)等等。 |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>函数 vs.Web 作业
我们可以一起讨论 Azure Functions 和 Azure 应用服务 WebJobs，因为它们都是代码优先的集成服务，且是为开发人员设计的。 它们使你能够运行脚本或一段代码以响应各种事件，如[新存储 Blob](functions-bindings-storage.md) 或 [WebHook 请求](functions-bindings-http-webhook.md)。 以下是它们的相似之处： 

* 二者都是在 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md) 上构建的，并享有[源控件](../app-service-web/app-service-continuous-deployment.md)[身份验证](../app-service/app-service-authentication-overview.md) 和 [监视](../app-service-web/web-sites-monitor.md)。
* 二者均是专门针对开发人员的服务。
* 二者均支持标准的脚本和编程语言。
* 二者均具有 NuGet 和 NPM 支持。

函数是对 WebJobs 的自然演化，它保留了 WebJobs 中最佳的内容，并进行了改进。 这些改进包括： 

* 直接在浏览器中进行简化的开发、测试和代码的运行。
* 内置集成有多个 Azure 服务和第三方服务，如 [GitHub WebHook](https://developer.github.com/webhooks/creating/)。
* 按使用付费，无需为[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)付费。
* 自动进行，[动态缩放](functions-scale.md)。
* 对于应用服务的现有客户，在应用服务计划上运行仍有可能（充分利用未充分利用的资源）。
* 集成有逻辑应用。

下表汇总了函数和 WebJobs 之间的差异：

|  | 函数 | Web 作业 |
| --- | --- | --- |
| 扩展 |无配置缩放 |应用服务计划的缩放 |
| 定价 |按使用或作为应用服务计划的部分付费 |应用服务计划的部分 |
| 运行类型 |触发、计划（通过计时器触发器） |触发的、连续的、计划的 |
| 触发事件 |[计时器](functions-bindings-timer.md)、[Azure Cosmos DB](functions-bindings-documentdb.md)、[Azure 事件中心](functions-bindings-event-hubs.md)、[HTTP/WebHook（GitHub、Slack）](functions-bindings-http-webhook.md)、[Azure 应用服务移动应用](functions-bindings-mobile-apps.md)、[Azure 通知中心](functions-bindings-notification-hubs.md)、[Azure 服务总线](functions-bindings-service-bus.md)、[Azure 存储](functions-bindings-storage.md) |[Azure 存储](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)、[Azure 服务总线](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| 浏览器开发 |受支持 | 不支持 |
| Windows 脚本（.cmd、.bat） |实验 |受支持 |
| PowerShell |实验 |受支持 |
| C# |受支持 |受支持 |
| F# |受支持 |不支持 |
| Bash |实验 |受支持 |
| PHP |实验 |受支持 |
| Python |实验 |受支持 |
| JavaScript |受支持 |受支持 |

要使用函数，还是要使用 WebJobs，这最终取决于已对应用服务执行何操作。 如果已有想为其运行代码片段的应用服务应用，且想要在同一 DevOps 环境中同时管理它们，请使用 WebJobs。 在以下情况下，请使用 Functions。

* 希望为其他 Azure 服务或第三方应用运行代码片段。
* 希望独立于应用服务应用管理集成代码。
* 希望从逻辑应用调用代码片段。 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>一起使用流、逻辑应用和函数
如前文所述，哪个服务最适合取决于具体情况。 

* 对于简单的业务优化，请使用 Flow。
* 如果集成方案相对流太过高级，或者需要 DevOps 功能和安全合规性，则使用逻辑应用。
* 如果集成方案中的某个步骤需要高度自定义的转换或专门的代码，请编写一个函数，并在逻辑应用中作为一个操作触发函数。

可以在流中调用逻辑应用。 也可以在逻辑应用中调用函数，在函数中调用逻辑应用。 随着时间的推移，Flow、逻辑应用和 Functions 之间的集成将得到进一步改进。 可以在某服务中构建一些项，并将其用于其他服务。 因此，在这三项技术中所做的任何投资都是值得的。

## <a name="next-steps"></a>后续步骤
通过创建第一个流、逻辑应用、函数应用或 WebJob，开始使用每个服务。 单击以下任一链接：

* [开始使用 Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)
* [使用 Visual Studio 部署 WebJobs](../app-service-web/websites-dotnet-deploy-webjobs.md)

或者，通过以下链接获取有关这些集成服务的详细信息：

* [利用 Azure Functions 和 Azure 应用服务实现集成方案，来自 Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [集成简化，来自 Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [逻辑应用实时网络广播](http://aka.ms/logicappslive)
* [Microsoft Flow 的常见问题](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Azure WebJobs 文档资源](../app-service-web/websites-webjobs-resources.md)


