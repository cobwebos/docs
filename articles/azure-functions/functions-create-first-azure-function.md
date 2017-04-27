---
title: "通过 Azure 门户创建第一个函数 | Microsoft Docs"
description: "欢迎使用 Azure。 通过 Azure 门户创建第一个 Azure 函数。"
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
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>在 Azure 门户中创建第一个函数

本主题演示如何使用 Azure Functions 创建由 HTTP 请求调用的“hello world”函数。 在 Azure 门户中创建函数前，必须创建 Function App，用于托管函数的无服务器执行。

若要完成本快速入门，必须具有一个 Azure 帐户。 提供[免费帐户](https://azure.microsoft.com/free/)。 还可以[试用 Azure Functions](https://azure.microsoft.com/try/app-service/functions/)，无需注册 Azure。

## <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-function-app"></a>创建 Function App

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

有关详细信息，请参阅[通过 Azure 门户创建 Function App](functions-create-function-app-portal.md)。

## <a name="create-a-function"></a>创建函数
这些步骤通过使用 Azure Functions 快速入门在新的 Function App 中创建函数。

1. 单击“新建”按钮，单击“WebHook + API”，选择函数的语言，然后单击“创建函数”。 使用 HTTP 触发函数模板，采用所选语言创建函数。  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

创建函数之后，可以通过发送 HTTP 请求对它进行测试。

## <a name="test-the-function"></a>测试函数

由于函数模板包含工作代码，因此可以直接在门户中立即测试新函数权限。

1. 在 Function App 中，单击新函数并查看模板中的代码。 请注意，函数需要具有在消息正文或查询字符串中传递的 name 值的 HTTP 请求。 该函数运行时，此值在响应消息中返回。 显示的示例是一个 JavaScript 函数。
   
2. 单击“运行”以运行该函数。 将看到执行由测试 HTTP 请求触发，信息写入日志且“hello...”响应在“测试”选项卡的“输出”中显示。
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. 在“请求正文”文本框中，将 name 属性的值更改为你的姓名，然后再次单击“运行”。 这次“输出”中的响应会包含你的姓名。   

4. 若要从 HTTP 测试工具或其他浏览器窗口触发相同函数的执行，请单击“</> 获取函数 URL”，复制请求 URL 并粘贴到工具或浏览器地址栏中。 将查询字符串值 `&name=yourname` 追加到 URL 并执行请求。 已向日志写入相同的信息，而响应消息正文中也包含相同的字符串。

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>后续步骤
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


