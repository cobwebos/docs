---
title: 常见方案 & 示例
description: 查找适用于 Azure 逻辑应用的示例、常见方案、教程和演练
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164621"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure 逻辑应用的常见方案、示例、教程和演练

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)通过提供[数百个随时可用的连接器](../connectors/apis-list.md)（范围从本地 SQL Server 或 SAP 到 Azure 认知服务），帮助你安排和集成不同的服务。 逻辑应用服务“无服务器”，因此无需担心规模或实例。 只需定义带有触发器的工作流和工作流执行的操作。 基础平台处理缩放、可用性和性能。 逻辑应用对于需要跨多个系统和服务协调操作的用例和方案特别有用。

为帮助你了解 Azure 逻辑应用支持的功能和模式，本文介绍了一些常见的起点、示例和方案。

## <a name="common-starting-points-for-logic-app-workflows"></a>逻辑应用工作流的常见开始点

每个逻辑应用均由[触发器*启动，并且只有一个触发器，该触发器可启动逻辑应用工作流并将任何数据作为该触发器的一部分进行传递*](../logic-apps/logic-apps-overview.md#logic-app-concepts)。 某些连接器提供触发器，类型包括：

* 轮询触发器：定期检查服务终结点以获取新数据。 如果存在新数据，触发器会创建并运行新的工作流实例（将数据作为输入）。

* 推送触发器：侦听服务终结点上的数据并等到特定事件发生。 事件发生时触发器会立即触发，创建并运行新的工作流实例（将任何可用数据用作输入）。

下面是描述常用触发器的示例：

* *轮询*触发器：

  * 利用[**定期**触发器](../connectors/connectors-native-recurrence.md)，你可以设置开始日期和时间以及用于触发逻辑应用的重复周期。 例如，可以选择在星期几和一天中某个时间触发逻辑应用。 有关详细信息，请参阅以下主题：<p>

    * [使用 Azure 逻辑应用计划和运行反复出现的自动化任务、流程和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [教程：使用 Azure 逻辑应用创建基于计划的自动化定期工作流](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **收到电子邮件时**，触发器允许逻辑应用检查逻辑应用支持的任何邮件提供商提供的新电子邮件，例如[Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、 [Gmail](https://docs.microsoft.com/connectors/gmail/)、 [Outlook.com](https://docs.microsoft.com/connectors/outlook/)等。 有关详细信息，请参阅以下主题：<p>

    * [教程：使用 Azure 逻辑应用创建自动化的基于审批的工作流](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [教程：使用 Azure 逻辑应用、Azure Functions 和 Azure 存储来自动处理电子邮件](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http**触发器](../connectors/connectors-native-http.md)可以通过 http 或 HTTPS 调用服务终结点。 有关详细信息，请参阅[使用 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

* *推送*触发器：

  * [ **Request**触发器](../connectors/connectors-native-reqres.md)可接收传入的 HTTPS 请求。

  * [HTTP Webhook 触发器**通过向该服务注册回叫 URL 来订阅服务终结点**](../connectors/connectors-native-webhook.md)。 这样一来，服务可只在指定事件发生时通知触发器，触发器便无需轮询服务。

在指定的事件发生之后，触发器将触发，这会创建一个新的逻辑应用工作流实例，并在工作流中运行这些操作。 通过工作流可访问触发器中任何数据。 例如，**新推文触发器上**的 Twitter 会将推文内容传递到逻辑应用运行中。 若要开始 Azure 逻辑应用，请尝试以下快速入门主题：

* [快速入门：使用 Azure 逻辑应用创建第一个自动化工作流-Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [快速入门：使用 Azure 逻辑应用创建自动化任务、进程和工作流-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [快速入门：使用 Visual Studio Code 创建和管理自动化逻辑应用工作流](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>控制流和错误处理功能

逻辑应用包括高级控制流（例如条件、开关、循环和作用域）的丰富功能。 若要确保解决方案具有弹性，还可以在工作流中实现错误和异常处理。

* 基于[条件语句](../logic-apps/logic-apps-control-flow-conditional-statement.md)和 [Switch 语句](../logic-apps/logic-apps-control-flow-switch-statement.md)执行不同操作
* [使用循环重复执行步骤或处理数组和集合中的项](../logic-apps/logic-apps-control-flow-loops.md)
* [使用范围将操作组合在一起](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [向工作流添加错误和异常处理](../logic-apps/logic-apps-exception-handling.md)
* [用例：医疗保健公司如何将逻辑应用异常处理用于 HL7 FHIR 工作流](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>创建自定义 Api 和连接器

对于没有已发布连接器的系统和服务，还可以扩展逻辑应用。

* [创建自定义 Api 以从 Azure 逻辑应用调用](../logic-apps/logic-apps-create-api-app.md)
* [使用轮询触发器模式定期检查是否有新数据或事件](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [使用 webhook 触发器模式等待和侦听新数据或事件](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [使用轮询操作模式执行长时间运行的任务](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [使用 webhook 操作模式执行长时间运行的任务](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure 逻辑应用中的自定义连接器](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>构建企业到企业（B2B）解决方案

对于企业集成解决方案和组织之间的无缝通信，可以通过将 Enterprise Integration Pack （EIP）与 Azure 逻辑应用配合使用，为这些方案生成自动化的可缩放工作流。 尽管组织使用不同的协议和格式，但它们仍可以通过电子方式交换消息。 EIP 将不同的格式转换为你的组织的系统可以处理并支持行业标准协议（包括 AS2、X12、EDIFACT 和 RosettaNet）的格式。 若要生成这些解决方案，请创建一个集成帐户，该帐户是一个单独的 Azure 资源，为你定义的项目提供安全、可缩放且可管理的容器，并可用于你的逻辑应用工作流。 例如，项目包括贸易合作伙伴、协议、地图、架构、证书和批配置。

* [概述： Azure 逻辑应用和 Enterprise Integration Pack B2B 企业集成解决方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [在 Azure 逻辑应用中创建和管理 B2B 企业集成的集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>访问 Azure 虚拟网络资源

有时，逻辑应用和集成帐户需要访问 Azure 虚拟网络中受保护的资源，例如虚拟机（Vm）以及其他系统或服务。 若要设置此访问权限，你可以创建一个可在其中生成并运行逻辑应用的 integration service 环境（ISE）。 ISE 是逻辑应用服务的专用和隔离的实例，它使用特定的资源（如存储），并独立于公用 "全局"、"多租户逻辑应用服务" 运行。 分离隔离的私有实例和公共全局实例还有助于降低其他 Azure 租户对应用性能的影响，这也称为 "干扰邻居"。

* [概述：从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>部署、管理和监视逻辑应用

可以使用 Visual Studio、Azure DevOps，或任何其他源代码管理和自动生成工具充分开发和部署逻辑应用。 为了支持资源模板中工作流和从属连接的部署，逻辑应用使用 Azure 资源部署模板。 Visual Studio 工具会自动生成这些模板，可以在源代码管理中签入这些模板以进行版本控制。 如需有关工作流运行状态的通知和诊断日志，Azure 逻辑应用还提供了监视和警报。

### <a name="deploy"></a>部署

* [快速入门：使用 Azure 逻辑应用创建自动化任务、进程和工作流-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [概述：自动化逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [创建 Azure 资源管理器模板以便自动部署 Azure 逻辑应用](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [部署 Azure 逻辑应用的 Azure 资源管理器模板](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [示例：从 azure 逻辑应用连接到 Azure 服务总线队列，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到 azure 存储帐户，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到集成帐户，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [示例：使用 Azure 逻辑应用协调 Azure Pipelines](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>管理

* [使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [为 B2B 企业集成创建和管理集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [在 Azure 逻辑应用中管理 integration service 环境（ISE）](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>监视

* [监视运行状态，查看触发器历史记录，并设置 Azure 逻辑应用的警报](../logic-apps/monitor-logic-apps.md)
* [设置 Azure Monitor 日志并收集 Azure 逻辑应用的诊断数据](../logic-apps/monitor-logic-apps-log-analytics.md)
* [设置在 Azure 逻辑应用中为 B2B 消息 Azure Monitor 日志和收集诊断数据](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [查看并创建用于在 Azure 逻辑应用 Azure Monitor 日志中监视和跟踪的查询](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>处理内容类型、转换和转换

可以使用 Azure 逻辑应用[工作流定义语言](https://aka.ms/logicappsdocs)中的多个函数访问、转换和变换多个内容类型。 例如，可以使用 `@json()` 和 `@xml()` 工作流表达式在字符串、JSON 和 XML 之间转换。 逻辑应用引擎通过保留内容类型，来支持在服务之间以无损方式进行内容传输。

* [在 Azure 逻辑应用中处理内容类型](../logic-apps/logic-apps-content-type.md)，如 `application/`、`application/octet-stream`和 `multipart/formdata`
* [在 Azure 逻辑应用和电源自动化的表达式中使用函数的参考指南](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure 逻辑应用的工作流定义语言架构](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>其他集成和功能

Azure 逻辑应用集成了许多服务，例如 Azure Functions、Azure API 管理、Azure App Service 和自定义 HTTP 终结点，例如 REST 和 SOAP。

* [从 Azure 逻辑应用调用 Azure Functions](../logic-apps/logic-apps-azure-functions.md)
* [教程：使用 Azure Functions 和 Azure 服务总线调用或触发逻辑应用](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [教程：使用 Azure 逻辑应用和 Azure Functions 创建流式处理客户见解仪表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [教程：创建与 Azure 逻辑应用和 Azure 认知服务集成的函数以分析 Twitter post 情绪](../azure-functions/functions-twitter-email.md)
* [教程：使用 Power BI 和 Azure 逻辑应用构建支持 AI 的社交仪表板](https://aka.ms/logicappsdemo)
* [教程：使用 Azure 事件网格和逻辑应用监视虚拟机更改](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [教程：通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [博客：使用 Azure 逻辑应用调用 SOAP 服务](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端到端方案

* [白皮书：端到端案例管理与 Azure 服务（如逻辑应用）的集成](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>客户案例

了解 Azure 逻辑应用以及其他 Azure 服务和 Microsoft 产品如何通过简化、组织、自动执行和协调复杂流程帮助[这些公司](https://aka.ms/logic-apps-customer-stories)提高其灵活性并集中精力处理其核心业务。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用的连接器](../connectors/apis-list.md)
* 了解[Azure 逻辑应用的 B2B 企业集成方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
