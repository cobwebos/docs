---
title: 示例和常见方案 - Azure 逻辑应用 | Microsoft Docs
description: 通过示例、方案、教程和演练深入了解逻辑应用
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/18
ms.author: LADocs; jehollan
ms.openlocfilehash: 02690284e5487dba8e5fb5d973958f944bfa339f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure 逻辑应用的常见方案、示例、教程和演练

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)提供 [100 多个可以使用的连接器](../connectors/apis-list.md)（从本地 SQL Server 或 SAP 到 Microsoft 认知服务），可帮助用户安排和集成不同的服务。 逻辑应用服务“无服务器”，因此无需担心规模或实例。 只需定义带有触发器的工作流和工作流执行的操作。 基础平台处理缩放、可用性和性能。 逻辑应用尤其适用于需要在多个系统中协调多个操作的用例和方案。

为了帮助你详细了解 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)支持的多种模式和功能，下面提供了一些常见示例和方案。

## <a name="popular-starting-points-for-logic-app-workflows"></a>逻辑应用工作流的常用起点

每个逻辑应用均由[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)启动，并且只有一个触发器，该触发器可启动逻辑应用工作流并将任何数据作为该触发器的一部分进行传递。 某些连接器提供触发器，类型包括：

* 轮询触发器：定期检查服务终结点以获取新数据。 如果存在新数据，触发器会创建并运行新的工作流实例（将数据作为输入）。

* 推送触发器：侦听服务终结点上的数据并等到特定事件发生。 事件发生时触发器会立即触发，创建并运行新的工作流实例（将任何可用数据用作输入）。

下面提供了几个常用触发器示例：

* 轮询： 

  * 使用[计划 - 重复触发器](../connectors/connectors-native-recurrence.md)可设置启动日期和时间以及触发逻辑应用的重复周期。 
  例如，可以选择在星期几和一天中某个时间触发逻辑应用。

  * “收到电子邮件时”触发器允许逻辑应用从逻辑应用支持的任何邮件提供程序检查是否有新的电子邮件，例如 [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、[Gmail](https://docs.microsoft.com/connectors/gmail/) 和 [Outlook.com](https://docs.microsoft.com/connectors/outlook/) 等。

  * [HTTP 触发器](../connectors/connectors-native-http.md)允许逻辑应用通过使用 HTTP 进行通信来检查指定的服务终结点。
  
* 推送：

  * [请求/响应 - 请求触发器](../connectors/connectors-native-reqres.md)允许逻辑应用接收 HTTP 请求并以某种方式实时响应事件。

  * [HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)通过向该服务注册回叫 URL 来订阅服务终结点。 
  这样一来，服务可只在指定事件发生时通知触发器，触发器便无需轮询服务。

收到有关新数据或事件的通知后，触发器随即触发，创建新的逻辑应用工作流实例并在工作流中运行操作。 通过工作流可访问触发器中任何数据。 例如，“发布新推文时”触发器会将推文内容传递到逻辑应用运行。 

## <a name="respond-to-triggers-and-extend-actions"></a>响应触发器和扩展操作

对于可能未发布连接器的系统和服务，也可以扩展逻辑应用。

* [创建自定义触发器或操作](../logic-apps/logic-apps-create-api-app.md)
* [为工作流运行设置长时间运行的操作](../logic-apps/logic-apps-create-api-app.md)
* [使用 webhook 响应外部事件和操作](../logic-apps/logic-apps-create-api-app.md)
* [利用对 HTTP 请求的同步响应调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)
* [教程：在几分钟内使用逻辑应用和 Power BI 生成由 AI 提供支持的社交仪表板](http://aka.ms/logicappsdemo)
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

可以使用 Visual Studio、Visual Studio Team Services，或任何其他源代码管理和自动生成工具充分开发和部署逻辑应用。 为了支持资源模板中工作流和从属连接的部署，逻辑应用使用 Azure 资源部署模板。 Visual Studio 工具会自动生成这些模板，可以在源代码管理中签入这些模板以进行版本控制。

* [使用 Visual Studio 创建和部署逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [在现有的逻辑应用上启用监视、日志记录和警报](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [创建自动部署模板](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>运行中的内容类型转换和变换

可以使用 Azure 逻辑应用[工作流定义语言](http://aka.ms/logicappsdocs)中的多个函数访问、转换和变换多个内容类型。 例如，可以使用 `@json()` 和 `@xml()` 工作流表达式在字符串、JSON 和 XML 之间转换。 逻辑应用引擎通过保留内容类型，来支持在服务之间以无损方式进行内容传输。

* [工作流表达式在逻辑应用中的工作原理](../logic-apps/logic-apps-author-definitions.md)
* [处理非 JSON 内容类型](../logic-apps/logic-apps-content-type.md)，如 `application/xml`、`application/octet-stream` 和 `multipart/formdata`
* [Azure 逻辑应用的工作流定义语言架构](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>其他集成和功能

逻辑应用还提供了与许多服务，如 Azure 函数、Azure API 管理、Azure 应用服务和自定义 HTTP 终结点（例如，REST 和 SOAP）的集成。

* [使用 Azure Serverless 创建实时社交仪表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [从逻辑应用调用 Azure 函数](../logic-apps/logic-apps-azure-functions.md)
* [教程：使用 Azure Functions 触发逻辑应用](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [教程：创建与 Azure 逻辑应用和 Microsoft 认知服务集成的函数以分析 Twitter 帖子人气](../azure-functions/functions-twitter-email.md)
* [教程：通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [博客：从逻辑应用调用 SOAP 终结点](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端到端方案

* [白皮书：端到端案例管理与 Azure 服务（如逻辑应用）的集成](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>客户案例

了解 Azure 逻辑应用以及其他 Azure 服务和 Microsoft 产品如何通过简化、组织、自动执行和协调复杂流程帮助[这些公司](https://aka.ms/logic-apps-customer-stories)提高其灵活性并集中精力处理其核心业务。

## <a name="next-steps"></a>后续步骤

* [使用 JSON 根据逻辑应用定义生成](../logic-apps/logic-apps-author-definitions.md)
* [在逻辑应用中处理错误和异常](../logic-apps/logic-apps-exception-handling.md)
* [就如何改进 Azure 逻辑应用提交评论、问题、反馈或建议](https://feedback.azure.com/forums/287593-logic-apps)