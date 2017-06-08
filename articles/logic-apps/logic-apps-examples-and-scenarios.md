---
title: "示例和方案 - Azure 逻辑应用 | Microsoft Docs"
description: "查看常见方案的逻辑应用示例"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: .net,nodejs,java
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/14/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dcf089d680249d0a2f9d748b315076d91c8c78e8
ms.contentlocale: zh-cn
ms.lasthandoff: 03/30/2017


---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Azure 逻辑应用的示例和常见方案

为帮助你详细了解 Azure 逻辑应用中的许多模式和功能，下面提供了一些常见示例和方案。

## <a name="key-scenarios-for-logic-apps"></a>逻辑应用的主要方案

Azure 逻辑应用为不同服务提供弹性业务流程和集成。 逻辑应用服务“无服务器”，因此无需担心规模或实例 - 你只需定义工作流（触发器和操作）。 基础平台处理缩放、可用性和性能。 需要协调多个操作（尤其是在多个系统中）的任何方案是 Azure 逻辑应用的绝好用例。 下面是一些模式和示例。

## <a name="respond-to-triggers-and-extend-actions"></a>响应触发器和扩展操作

每个逻辑应用均以触发器开头。 例如，工作流可以从计划事件、手动调用或来自外部系统的事件（如“将文件添加到 FTP 服务器时”触发器）开始。 Azure 逻辑应用目前支持 100 多个可以使用的连接器（从本地 SAP 到 Azure 认知服务）。 对于可能未发布连接器的系统和服务，也可以扩展逻辑应用。

* [教程：在几分钟内使用逻辑应用和 Power BI 生成由 AI 提供支持的社交仪表板](http://aka.ms/logicappsdemo)
* [创建自定义触发器或操作](../logic-apps/logic-apps-create-api-app.md)
* [为工作流运行设置长时间运行的操作](../logic-apps/logic-apps-create-api-app.md)
* [使用 webhook 响应外部事件和操作](../logic-apps/logic-apps-create-api-app.md)
* [利用对 HTTP 请求的同步响应调用、触发或嵌套工作流](logic-apps-http-endpoint.md)
* [教程：响应 Twilio SMS webhook 并发送文本响应](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>错误处理、日志记录和控制流功能

逻辑应用包括高级控制流（例如条件、开关、循环和作用域）的丰富功能。 若要确保解决方案具有弹性，还可以在工作流中实现错误和异常处理。 如需有关工作流运行状态的通知和诊断日志，Azure 逻辑应用还提供了监视和警报。

* [使用 switch 语句执行不同操作](logic-apps-switch-case.md)
* [在逻辑应用中使用循环和批处理数组和集合中的项](logic-apps-loops-and-scopes.md)
* [工作流中的授权错误和异常处理](logic-apps-exception-handling.md)
* [配置 Azure 警报和诊断](logic-apps-monitor-your-logic-apps.md)
* [用例：医疗保健公司如何将逻辑应用异常处理用于 HL7 FHIR 工作流](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>部署和管理逻辑应用

可以使用 Visual Studio、Visual Studio Team Services，或任何其他源代码管理和自动生成工具充分开发和部署逻辑应用。 为了支持资源模板中工作流和从属连接的部署，逻辑应用使用 Azure 资源部署模板。 Visual Studio 工具会自动生成这些模板，你可以在源代码管理中签入这些模板以进行版本控制。

* [创建自动部署模板](../logic-apps/logic-apps-create-deploy-template.md)
* [从 Visual Studio 生成和部署逻辑应用](logic-apps-deploy-from-vs.md)
* [监视逻辑应用的运行状况](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>运行中的内容类型转换和变换

可以使用 Azure 逻辑应用[工作流定义语言](http://aka.ms/logicappsdocs)中的多个函数访问、转换和变换多个内容类型。 例如，可以使用 `@json()` 和 `@xml()` 工作流表达式在字符串、JSON 和 XML 之间转换。 逻辑应用引擎通过保留内容类型，来支持在服务之间以无损方式进行内容传输。

* [处理非 JSON 内容类型](../logic-apps/logic-apps-content-type.md)，如 `application/xml`、`application/octet-stream` 和 `multipart/formdata`
* [工作流表达式在逻辑应用中的工作原理](../logic-apps/logic-apps-author-definitions.md)
* [参考：Azure 逻辑应用工作流定义语言](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>其他集成和功能

逻辑应用还提供了与许多服务，如 Azure 函数、Azure API 管理、Azure 应用服务和自定义 HTTP 终结点（例如，REST 和 SOAP）的集成。

* [使用 Azure Serverless 创建实时社交仪表板](logic-apps-scenario-social-serverless.md)
* [从逻辑应用调用 Azure 函数](../logic-apps/logic-apps-azure-functions.md)
* [方案：使用 Azure Functions 触发逻辑应用](logic-apps-scenario-function-sb-trigger.md)
* [博客：从逻辑应用调用 SOAP 终结点](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="next-steps"></a>后续步骤

- [在逻辑应用中处理错误和异常](logic-apps-exception-handling.md)
- [使用工作流定义语言创作工作流定义](logic-apps-author-definitions.md)
- [就如何改进 Azure 逻辑应用提交你的评论、问题、反馈或建议](https://feedback.azure.com/forums/287593-logic-apps)
