---
title: "通过 Azure 门户创建函数 | Microsoft Docs"
description: "只需不到两分钟即可生成你的第一个 Azure Function（无服务器应用程序）。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 8d7823b6ecbc1a762839fc620f76d0bf4687d8fa
ms.openlocfilehash: 5b33a36a3415efd2bc4673999806aa797f4a1c9f


---
# <a name="create-a-function-from-the-azure-portal"></a>通过 Azure 门户创建函数
## <a name="overview"></a>概述
Azure Functions 提供事件驱动、按需计算体验，可以扩展现有的 Azure 应用程序平台，并能够实现由其他 Azure 服务、SaaS 产品以及本地系统中发生的事件触发的代码。 借助 Azure Functions，应用程序可以按需缩放，且仅需对消耗的资源付费。 Azure Functions 让你能够创建以各种编程语言实现的计划或触发代码单元。 若要了解有关 Azure Functions 的详细信息，请参阅 [Azure Functions 概述](functions-overview.md)。

本主题演示如何使用 Azure 门户创建由 HTTP 触发器调用的简单“hello world”Node.js Azure 函数。 在 Azure 门户中创建函数前，必须在 Azure App Service 中显示创建 Function App。 若要自动创建 Function App，请参阅[另一 Azure Functions 快速入门教程](functions-create-first-azure-function.md)，其为更简单的快速入门体验且包括视频。

## <a name="create-a-function-app"></a>创建 Function App
Function App 在 Azure 中托管函数的执行。 请按照下列步骤在 Azure 门户中创建 Function App。

必须先具有有效的 Azure 帐户，然后才能创建你的第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。

1. 转到 [Azure 门户](https://portal.azure.com)，然后使用你的 Azure 帐户登录。
2. 单击“+新建” > “计算” > “Function App”，选择你的订阅，键入用于标识函数应用的唯一**应用名称**，然后指定以下设置：
   
   * **[资源组](../azure-resource-manager/resource-group-overview.md)**：选择“新建”并为新的资源组输入名称。 还可选择现有资源组，但是可能无法为 Function App 创建基于使用情况的应用服务计划。
   * **[托管计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**，可以是下列其中一项： 
     * **使用计划**：Azure Functions 的默认计划类型。 选择使用计划时，还必须选择“位置”并设置“内存分配”（以 MB 为单位）。 有关内存分配如何影响成本的信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。 
     * **应用服务计划**：应用服务计划要求创建**应用服务计划/位置**或选择现有应用服务计划/位置。 这些设置将确定与应用关联的[位置、功能、成本和计算资源](https://azure.microsoft.com/pricing/details/app-service/)。  
   * **存储帐户**：每个 Function App 都需要存储帐户。 你可以使用现有存储帐户，也可以创建一个存储帐户。 
     
     ![在 Azure 门户中创建新的 Function App](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. 单击“创建”来预配和部署新的 Function App。  

预配 Function App 后，可创建自己的第一个函数。

## <a name="create-a-function"></a>创建函数
这些步骤从 Azure Functions 快速入门中创建函数。

1. 在“快速入门”选项卡中，单击“WebHook + API”和“JavaScript”，然后单击“创建函数”。 将创建新的预定义 Node.js 函数。 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. （可选）此时，在快速入门中，可以选择在门户内进行 Azure Functions 功能快速浏览。    完成或跳过浏览后，可以使用 HTTP 触发器测试新函数。

## <a name="test-the-function"></a>测试函数
Azure Functions 快速入门包含功能性代码，因此可以立即测试新函数。

1. 在“开发”选项卡中，查看“代码”窗口，将会发现此 Node.js 代码需要具有在消息正文或查询字符串中传递的 name 值的 HTTP 请求。 该函数运行时，此值在响应消息中返回。
   
2. 单击“测试”显示函数的内置 HTTP 测试请求窗格。
 
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. 在“请求正文”文字框中，将 *name* 属性的值更改为你的名称，然后单击“运行”。 将看到执行由测试 HTTP 请求触发，信息写入流式处理日志且“hello”响应在“输出”中显示。 

3. 若要从其他浏览器窗口或选项卡触发相同函数的执行，请从“开发”选项卡复制“函数 URL”值并粘贴到浏览器地址栏中。 将查询字符串值 `&name=yourname` 追加到 URL 并按 Enter。 相同的信息会写入日志，浏览器会像之前一样显示“hello”响应。

## <a name="next-steps"></a>后续步骤
本快速入门演示一个简单的基本 HTTP 触发函数执行。 若要详细了解如何在应用中使用 Azure Functions，请参阅以下主题：

* [Azure Functions 开发人员参考](functions-reference.md)  
  ，用于编码函数和定义触发器及绑定的程序员参考。
* [测试 Azure Functions](functions-test-a-function.md)  
   介绍可用于测试函数的各种工具和技巧。
* [如何缩放 Azure Functions](functions-scale.md)  
  讨论 Azure Functions 提供的服务计划（包括基于使用情况的应用服务计划）以及如何选择合适的计划。 
* [什么是 Azure App Service？](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions 使用 Azure App Service 平台执行核心功能，例如部署、环境变量和诊断。 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


