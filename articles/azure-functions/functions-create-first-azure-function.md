---
title: "创建第一个 Azure 函数 | Microsoft Docs"
description: "只需不到两分钟即可生成你的第一个 Azure Function（无服务器应用程序）。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>创建你的第一个 Azure 函数
## <a name="overview"></a>概述
Azure Functions 提供事件驱动、按需计算体验，可以扩展现有的 Azure 应用程序平台，并能够实现由其他 Azure 服务、SaaS 产品以及本地系统中发生的事件触发的代码。 借助 Azure Functions，应用程序可以按需缩放，且仅需对消耗的资源付费。 Azure Functions 让你能够创建以各种编程语言实现的计划或触发代码单元。 若要了解有关 Azure Functions 的详细信息，请参阅 [Azure Functions 概述](functions-overview.md)。

本主题演示如何在门户中使用 Azure Functions 快速入门，创建由 HTTP 触发器调用的简单“hello world”JavaScript 函数。 还可观看简短视频，了解如何在门户中执行这些步骤。

## <a name="watch-the-video"></a>观看视频
以下视频演示如何执行本教程中的基本步骤。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>通过快速入门创建函数
Function App 在 Azure 中托管函数的执行。 请遵循以下步骤创建包含新函数的 Function App。 创建的 Function App 使用默认配置。 有关如何显式创建 Function App 的示例，请参阅 [其他 Azure Functions 快速入门教程](functions-create-first-azure-function-azure-portal.md)。

必须先具有有效的 Azure 帐户，然后才能创建你的第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。

1. 转到 [Azure Functions 门户](https://functions.azure.com/signin) ，然后使用你的 Azure 帐户登录。
2. 键入新 Function App 的唯一“名称”或接受自动生成的名称，选择首选“区域”，然后单击“创建 + 开始使用”。 请注意，必须输入有效的名称，其中只能包含字母、数字和连字符。 下划线 (**_**) 是不允许的字符。
3. 在“快速入门”选项卡中，单击“WebHook + API”和“JavaScript”，然后单击“创建函数”。 将创建新的预定义 JavaScript 函数。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. （可选）此时，在快速入门中，可以选择在门户内进行 Azure Functions 功能快速浏览。 完成或跳过浏览后，可以使用 HTTP 触发器测试新函数。

## <a name="test-the-function"></a>测试函数
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


