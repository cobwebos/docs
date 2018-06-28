---
title: 比较流、逻辑应用、函数和 WebJobs - Azure
description: 比较针对集成任务进行了优化的各种 Microsoft 云服务：流、逻辑应用、函数和 WebJobs。
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow、流、逻辑应用、azure functions、函数、azure webjobs、webjobs，事件处理、动态计算、无服务器体系结构
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 9b9ef150a86a754d4ed328184eb02c1937008b7b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028857"
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>比较流、逻辑应用、函数和 WebJobs

本文比较了以下 Microsoft 云服务：

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure 应用服务 WebJobs](../app-service/web-sites-create-web-jobs.md)

所有这些服务都可以解决集成问题并自动化业务流程。 它们都可以定义输入、操作、条件和输出。 可以在日程安排或触发器中运行其中一个。 但是，每种服务都有其独特的优点，本文将介绍这些差异。

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>比较 Microsoft Flow 和 Azure 逻辑应用

流和逻辑应用都是可以创建工作流的“设计器优先”集成服务。 这两种服务都与各种 SaaS 和企业应用程序相集成。 

流构建在逻辑应用之上。 它们有相同的工作流设计器和相同的[连接器](../connectors/apis-list.md)。 

借助流，任何办公室工作人员都可以执行简单的集成（例如，对 SharePoint 文档库的审批过程），无需求助开发人员或 IT 部门。 另一方面，逻辑应用可启用需要企业级 DevOps 和安全实践的高级集成（例如 B2B 流程）。 对于业务工作流，其典型特征就是复杂性会随时间增长而增加。 相应地，可以先从流开始，然后根据需要将其转换到逻辑应用。

下表有助于确定流或逻辑应用是否最适合给定的集成。

|  | 流向 | 逻辑应用 |
| --- | --- | --- |
| 用户 |办公人员、企业用户、SharePoint 管理员 |Pro 集成人员和开发人员、IT 专业人员 |
| 方案 |自助服务 |高级集成 |
| 设计工具 |浏览器内和移动应用、仅 UI |浏览器和 [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)、[代码视图](../logic-apps/logic-apps-author-definitions.md)可用 |
| 应用程序生命周期管理 (ALM) |在非生产环境中进行设计和测试，并在准备就绪后推向生产。 |DevOps：[Azure 资源管理](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)中的源代码管理、测试、支持、自动化和可管理性 |
| 管理员体验 |管理流环境和数据丢失防护 (DLP) 策略，跟踪许可 [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |管理资源组、连接、访问管理和日志记录 [https://portal.azure.com](https://portal.azure.com) |
| “安全” |Office 365 安全性和符合性审核日志、数据丢失防护 (DLP)、针对敏感数据的[静态加密](https://wikipedia.org/wiki/Data_at_rest#Encryption)等。 |Azure 的安全保证：[Azure 安全性](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)、[安全中心](https://azure.microsoft.com/services/security-center/)、[审核日志](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)等等。 |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>比较 Azure Functions 和 Azure 逻辑应用

函数和逻辑应用是用于启用无服务器工作负荷的 Azure 服务。 Azure Functions 是一种无服务器计算服务，而 Azure 逻辑应用提供无服务器工作流。 这两种服务都可以创建复杂“业务流程”。 业务流程是函数或步骤（在逻辑应用中称为“操作”）的集合，将执行这些函数或步骤来完成复杂任务。 例如，若要处理一批订单，可以并行执行某个函数的许多实例，等待所有实例完成，然后执行某个函数来计算聚合结果。

对于 Azure Functions，你通过编写代码并使用 [Durable Functions 扩展](durable-functions-overview.md)（预览版）来开发业务流程。 对于逻辑应用，你通过使用 GUI 或通过编辑配置文件来创建业务流程。

在构建业务流程、从逻辑应用中调用函数以及从函数中调用逻辑应用时，可以混合使用各种服务。 可以根据服务功能或你的个人喜好选择如何构建每个业务流程。 下表列出了这些服务之间的一些主要区别：
 
|  | Durable Functions | 逻辑应用 |
| --- | --- | --- |
| 开发 | 代码优先（命令性） | 设计器优先（声明性） |
| 连接 | [有关十多个内置的绑定类型](functions-triggers-bindings.md#supported-bindings)，为自定义绑定编写代码 | [大型连接器集合](../connectors/apis-list.md)、[适用于 B2B 方案的 Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)、[构建自定义连接器](../logic-apps/custom-connector-overview.md) |
| 操作 | 每个活动都是一个 Azure 函数；为活动函数编写代码 |[现成操作的大型集合](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| 监视 | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)、[Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)、[Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| 管理 | [REST API](durable-functions-http-api.md)、[Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)、[REST API](https://docs.microsoft.com/rest/api/logic/)、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0)、[Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| 执行上下文 | 可以[在本地](functions-runtime-overview.md)或在云中运行。 | 只能在云中运行。|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>比较函数和 WebJobs

与 Azure Functions 一样，包含 WebJobs SDK 的 Azure 应用服务是一项代码优先的集成服务，专为开发人员设计。 二者都是在 [Azure 应用服务](../app-service/app-service-web-overview.md) 上构建的，支持[源代码管理集成](../app-service/app-service-continuous-deployment.md)、[身份验证](../app-service/app-service-authentication-overview.md)以及[使用 Application Insights 集成进行监视](functions-monitoring.md)等功能。

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs 和 WebJobs SDK

可以使用应用服务的 *WebJobs* 功能，在应用服务 Web 应用上下文中运行脚本或代码。 *WebJobs SDK* 是一个为 WebJobs 设计的框架，可以简化为响应 Azure 服务中的事件而编写的代码。 例如，若要响应在 Azure 存储中创建映像 Blob 这一事件，可以创建一个缩略图。 WebJobs SDK 以 .NET 控制台应用程序的方式运行，可以部署到 WebJob。 

WebJobs 和 WebJobs SDK 在一起使用时效果最佳，但也可在没有 WebJobs SDK 的情况下使用 WebJobs，反之亦然。 WebJob 可以运行任何在应用服务沙盒中运行的程序或脚本。 WebJobs SDK 控制台应用程序可以在运行控制台应用程序的任何位置运行，例如本地服务器。

### <a name="comparison-table"></a>比较表

Azure Functions 是在 WebJobs SDK 上构建的，因此共享许多相同的事件触发器以及到其他 Azure 服务的连接。 在选择 Azure Functions 还是选择带 WebJobs SDK 的 WebJobs 时，请考虑下面一些因素：

|  | 函数 | 带 WebJobs SDK 的 WebJobs |
| --- | --- | --- |
|使用[自动缩放](functions-scale.md#how-the-consumption-plan-works)的[无服务器应用模型](https://azure.microsoft.com/overview/serverless-computing/)|✔||
|[在浏览器中进行开发和测试](functions-create-first-azure-function.md) |✔||
|[按使用付费定价](functions-scale.md#consumption-plan)|✔||
|[集成逻辑应用](functions-twitter-email.md)|✔||
| 触发事件 |[计时器](functions-bindings-timer.md)<br>[Azure 存储队列和 Blob](functions-bindings-storage-blob.md)<br>[Azure 服务总线队列和主题](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure 事件中心](functions-bindings-event-hubs.md)<br>[HTTP/WebHook（GitHub、Slack）](functions-bindings-http-webhook.md)<br>[Azure 事件网格](functions-bindings-event-grid.md)|[计时器](functions-bindings-timer.md)<br>[Azure 存储队列和 Blob](functions-bindings-storage-blob.md)<br>[Azure 服务总线队列和主题](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure 事件中心](functions-bindings-event-hubs.md)<br>[文件系统](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| 支持的语言  |C#<br>F#<br>JavaScript<br>Java（预览版） |C#<sup>1</sup>|
|包管理器|NPM 和 NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs（不带 WebJobs SDK）支持 C#、JavaScript、Bash、.cmd、.bat、PowerShell、PHP、TypeScript、Python 等。 这不是完整的列表；WebJob 可以运行任何程序或脚本，只要该程序或脚本可以在应用服务沙盒中运行。

<sup>2</sup> WebJobs（不带 WebJobs SDK）支持 NPM 和 NuGet。

### <a name="summary"></a>摘要

Azure Functions 可以改进开发人员工作效率，并提供更多的编程语言选项、更多的开发环境选项、更多的 Azure 服务集成选项，以及更多的定价选项。 大多数情况下，它是最佳选择。

下面两种情况最适合选择 WebJobs：

* 需要对侦听事件的代码（`JobHost` 对象）进行更多的控制。 若要在 [host.json](functions-host-json.md) 文件中自定义 `JobHost` 行为，则 Functions 提供的方式有限。 有时候，需要执行的操作无法在 JSON 文件中通过字符串来指定。 例如，只有 WebJobs SDK 允许配置 Azure 存储的自定义重试策略。
* 你已经有需要为其运行代码片段的应用服务应用，且需要在同一 DevOps 环境中同时管理它们。

对于其他需要运行代码片段来集成 Azure 或第三方服务的情况，请选择 Azure Functions 而不是带 WebJobs SDK 的 WebJobs。

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>流、逻辑应用、Functions 和 WebJobs 一起

不需只选择一项这样的服务；这些服务彼此集成，也与外部服务集成。

流可以调用逻辑应用。 逻辑应用可以调用函数，而函数也可以调用逻辑应用。 请参阅相关文档，例如，[创建与 Azure 逻辑应用集成的函数](functions-twitter-email.md)。

随着时间的推移，Flow、逻辑应用和 Functions 之间的集成将得到进一步改进。 可以在某服务中构建一些项，并将其用于其他服务。

## <a name="next-steps"></a>后续步骤

开始创建第一个流、逻辑应用、函数应用。 单击以下任一链接：

* [开始使用 Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)

或者，通过以下链接获取有关这些集成服务的详细信息：

* [利用 Azure Functions 和 Azure 应用服务实现集成方案，来自 Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [集成简化，来自 Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [逻辑应用实时网络广播](http://aka.ms/logicappslive)
* [Microsoft Flow 的常见问题](https://flow.microsoft.com/documentation/frequently-asked-questions/)
