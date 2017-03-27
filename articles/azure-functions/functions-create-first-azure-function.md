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
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>创建你的第一个 Azure 函数

本主题演示如何在门户中使用 Azure Functions 快速入门，创建由 HTTP 请求调用的简单“hello world”函数。 若要了解有关 Azure Functions 的详细信息，请参阅 [Azure Functions 概述](functions-overview.md)。

在开始之前，必须具有 Azure 帐户。 提供[免费帐户](https://azure.microsoft.com/free/)。 还可以[尝试 Azure Functions](https://azure.microsoft.com/try/app-service/functions/)，无需注册 Azure。

## <a name="create-a-function-from-the-portal-quickstart"></a>通过门户创建函数快速入门

1. 转到 [Azure Functions 门户](https://functions.azure.com/signin)，然后使用 Azure 帐户登录。 
 
2. 键入新 Function App 的唯一“名称”或接受自动生成的名称，选择首选“区域”，然后单击“创建 + 开始使用”。 有效名称只能包含字母、数字和连字符。 下划线 (**_**) 是不允许的字符。

3. 在“快速入门”选项卡中，单击“WebHook + API”，选择函数的语言，然后单击“创建函数”。 此时会在选择的语言中创建新的预定义函数。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. （可选）此时，在快速入门中，可以选择在门户内进行 Azure Functions 功能快速浏览。 完成或跳过浏览后，可以通过发送 HTTP 请求来测试新函数。

## <a name="test-the-function"></a>测试函数
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>观看视频
以下视频演示如何执行本教程中的基本步骤。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>后续步骤
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


