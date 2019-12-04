---
title: 常见方案 & 示例
description: 查找适用于 Azure 逻辑应用的示例、常见方案、教程和演练
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: ff6136dea8a25a39e045665d61bdd5d93eeb58a5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792137"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure 逻辑应用的常见方案、示例、教程和演练

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)通过提供[数百个随时可用的连接器](../connectors/apis-list.md)（范围从本地 SQL Server 或 SAP 到 Azure 认知服务），帮助你安排和集成不同的服务。 逻辑应用服务“无服务器”，因此无需担心规模或实例。 只需定义带有触发器的工作流和工作流执行的操作。 基础平台处理缩放、可用性和性能。 逻辑应用尤其适用于需要在多个系统中协调多个操作的用例和方案。

为了帮助你了解有关 Azure 逻辑应用支持的多种模式和功能的详细信息，请参阅下面的常见示例和方案。

## <a name="popular-starting-points-for-logic-app-workflows"></a>逻辑应用工作流的常用起点

每个逻辑应用均由[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)启动，并且只有一个触发器，该触发器可启动逻辑应用工作流并将任何数据作为该触发器的一部分进行传递。 某些连接器提供触发器，类型包括：

* 轮询触发器：定期检查服务终结点以获取新数据。 如果存在新数据，触发器会创建并运行新的工作流实例（将数据作为输入）。

* 推送触发器：侦听服务终结点上的数据并等到特定事件发生。 事件发生时触发器会立即触发，创建并运行新的工作流实例（将任何可用数据用作输入）。

下面提供了几个常用触发器示例：

* 轮询：

  * 利用[**定期**触发器](../connectors/connectors-native-recurrence.md)，你可以设置开始日期和时间以及用于触发逻辑应用的重复周期。 例如，可以选择在星期几和一天中某个时间触发逻辑应用。 有关详细信息，请参阅以下主题：

    * [通过 Azure 逻辑应用计划和运行定期自动化任务、进程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [教程：使用 Azure 逻辑应用按计划检查流量](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * “收到电子邮件时”触发器允许逻辑应用从逻辑应用支持的任何邮件提供程序检查是否有新的电子邮件，例如 [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、[Gmail](https://docs.microsoft.com/connectors/gmail/) 和 [Outlook.com](https://docs.microsoft.com/connectors/outlook/) 等。 有关详细信息，请参阅以下主题： 

    * [教程：通过 Azure 逻辑应用管理邮件列表请求](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [教程：通过 Azure 逻辑应用自动处理电子邮件和附件](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [HTTP 触发器](../connectors/connectors-native-http.md)允许逻辑应用通过使用 HTTP 进行通信来检查指定的服务终结点。
  
* 推送：

  * [ **Request**触发器](../connectors/connectors-native-reqres.md)允许逻辑应用接收 HTTP 请求，并以某种方式实时响应事件。

  * [HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)通过向该服务注册回叫 URL 来订阅服务终结点。 这样一来，服务可只在指定事件发生时通知触发器，触发器便无需轮询服务。

收到有关新数据或事件的通知后，触发器随即触发，创建新的逻辑应用工作流实例并在工作流中运行操作。 通过工作流可访问触发器中任何数据。 例如，“发布新推文时”触发器会将推文内容传递到逻辑应用运行。 若要开始 Azure 逻辑应用，请尝试以下快速入门主题：

* [快速入门：在 Azure 门户中通过 Azure 逻辑应用创建第一个自动化工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [快速入门：使用 Visual Studio 在 Azure 逻辑应用中创建自动化任务、进程和工作流](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [快速入门：使用 Visual Studio Code 创建和管理自动化逻辑应用工作流](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>响应触发器和扩展操作

对于可能未发布连接器的系统和服务，也可以扩展逻辑应用。

* [创建自定义触发器或操作](../logic-apps/logic-apps-create-api-app.md)
* [为工作流运行设置长时间运行的操作](../logic-apps/logic-apps-create-api-app.md)
* [使用 webhook 响应外部事件和操作](../logic-apps/logic-apps-create-api-app.md)
* [利用对 HTTP 请求的同步响应调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)
* [教程：在几分钟内使用逻辑应用和 Power BI 生成由 AI 提供支持的社交仪表板](https://aka.ms/logicappsdemo)
* [视频：响应 Twilio SMS Webhook 并发送文本响应](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>控制流、错误处理和日志记录功能

逻辑应用包括高级控制流（例如条件、开关、循环和作用域）的丰富功能。 若要确保解决方案具有弹性，还可以在工作流中实现错误和异常处理。 如需有关工作流运行状态的通知和诊断日志，Azure 逻辑应用还提供了监视和警报。

* 基于[条件语句](../logic-apps/logic-apps-control-flow-conditional-statement.md)和 [Switch 语句](../logic-apps/logic-apps-control-flow-switch-statement.md)执行不同操作
* [使用循环重复执行步骤或处理数组和集合中的项](../logic-apps/logic-apps-control-flow-loops.md)
* [使用范围将操作组合在一起](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [工作流中的授权错误和异常处理](../logic-apps/logic-apps-exception-handling.md)
* [用例：医疗保健公司如何将逻辑应用异常处理用于 HL7 FHIR 工作流](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [在现有的逻辑应用上启用监视、日志记录和警报](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [创建逻辑应用时启用监视和诊断日志记录](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>部署和管理逻辑应用

可以使用 Visual Studio、Azure DevOps，或任何其他源代码管理和自动生成工具充分开发和部署逻辑应用。 为了支持资源模板中工作流和从属连接的部署，逻辑应用使用 Azure 资源部署模板。 Visual Studio 工具会自动生成这些模板，可以在源代码管理中签入这些模板以进行版本控制。

* [使用 Visual Studio 创建和部署逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [在现有的逻辑应用上启用监视、日志记录和警报](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [自动部署逻辑应用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [示例：从 azure 逻辑应用连接到 Azure 服务总线队列，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到 azure 存储帐户，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到集成帐户，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>运行中的内容类型转换和变换

可以使用 Azure 逻辑应用[工作流定义语言](https://aka.ms/logicappsdocs)中的多个函数访问、转换和变换多个内容类型。 例如，可以使用 `@json()` 和 `@xml()` 工作流表达式在字符串、JSON 和 XML 之间转换。 逻辑应用引擎通过保留内容类型，来支持在服务之间以无损方式进行内容传输。

* [工作流表达式在逻辑应用中的工作原理](../logic-apps/logic-apps-author-definitions.md)
* [处理非 JSON 内容类型](../logic-apps/logic-apps-content-type.md)，如 `application/xml`、`application/octet-stream` 和 `multipart/formdata`
* [Azure 逻辑应用的工作流定义语言架构](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>其他集成和功能

逻辑应用还提供了与许多服务，如 Azure 函数、Azure API 管理、Azure 应用服务和自定义 HTTP 终结点（例如，REST 和 SOAP）的集成。

* [使用 Azure Serverless 创建实时社交仪表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [从逻辑应用调用 Azure 函数](../logic-apps/logic-apps-azure-functions.md)
* [教程：使用 Azure Functions 触发逻辑应用](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [教程：创建与 Azure 逻辑应用和 Azure 认知服务集成的函数以分析 Twitter post 情绪](../azure-functions/functions-twitter-email.md)
* [教程：通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [博客：从逻辑应用调用 SOAP 终结点](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端到端方案

* [白皮书：端到端案例管理与 Azure 服务（如逻辑应用）的集成](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>客户案例

了解 Azure 逻辑应用以及其他 Azure 服务和 Microsoft 产品如何通过简化、组织、自动执行和协调复杂流程帮助[这些公司](https://aka.ms/logic-apps-customer-stories)提高其灵活性并集中精力处理其核心业务。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用的连接器](../connectors/apis-list.md)
* 了解[Azure 逻辑应用的 B2B 企业集成方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
