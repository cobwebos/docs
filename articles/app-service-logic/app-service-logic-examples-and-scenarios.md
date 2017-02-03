---
title: "逻辑应用示例和方案 | Microsoft Docs"
description: "请参阅常见逻辑应用示例并了解如何实现常见方案"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 4fde1a22ac76306e79a68a37e110828da9a2ef13
ms.openlocfilehash: 3cf2d8b19dd5785317a382dc429feda02d8a2f20


---
# <a name="logic-apps-examples-and-common-scenarios"></a>逻辑应用示例和常见方案
本文档详细介绍了常见方案和示例，以帮助你了解一些使用逻辑应用自动执行业务流程的方式。 

## <a name="custom-triggers-and-actions"></a>自定义触发器和操作
可以通过几种方式从其他应用触发逻辑应用。 以下是几个常见示例：

* [创建自定义触发器或操作](app-service-logic-create-api-app.md)
* [长时间运行的操作](app-service-logic-create-api-app.md)
* [HTTP 请求触发器 (POST)](app-service-logic-http-endpoint.md)
* [Webhook 触发器和操作](app-service-logic-create-api-app.md)
* [轮询触发器](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>方案
* [请求同步响应](app-service-logic-http-endpoint.md)
* [使用 SMS 的请求响应](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>错误处理和日志记录
* [异常和错误处理](app-service-logic-exception-handling.md)
* [配置 Azure 警报和诊断](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>方案
* [用例：错误和异常处理](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>部署和管理
* [创建自动部署](app-service-logic-create-deploy-template.md)
* [从 Visual Studio 生成和部署逻辑应用](app-service-logic-deploy-from-vs.md)
* [监视逻辑应用](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>内容类型、转换和变换
逻辑应用[工作流定义语言](http://aka.ms/logicappsdocs)包含许多功能，使你可以转换和使用不同的内容类型。 此外，引擎会在数据流过工作流时，尽可能保留内容类型。

* [内容类型的处理](app-service-logic-content-type.md)，如应用程序/json、应用程序/xml 和文本/无格式
* [创作工作流定义](app-service-logic-author-definitions.md)
* [工作流定义语言参考](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>批处理和循环
* [SplitOn](app-service-logic-loops-and-scopes.md)
* [ForEach](app-service-logic-loops-and-scopes.md)
* [Until](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>与 Azure Functions 的集成
* [Azure Functions 集成](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>方案
* [作为服务总线触发器的 Azure 函数](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP、REST 和 SOAP
* [调用 SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

我们会继续向本文档添加示例和方案。 使用下面的评论部分可让我们了解你希望在此处看到哪些示例或方案。




<!--HONumber=Dec16_HO2-->


