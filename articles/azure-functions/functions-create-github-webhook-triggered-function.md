---
title: "在 Azure 中创建由 GitHub Webhook 触发的函数 | Microsoft Docs"
description: "使用 Azure Functions 创建由 GitHub WebHook 调用的无服务器函数。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>创建由 GitHub Webhook 触发的函数

了解如何创建一个由 GitHub Webhook 触发的函数。 

![在 Azure 门户中创建 Function App](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

本主题需要在[从 Azure 门户创建你的第一个函数](functions-create-first-azure-function.md)主题中创建的资源。

你还需要一个 GitHub 帐户。 可以[注册免费 GitHub 帐户](https://github.com/join)（如果还没有）。 

完成本主题中的所有步骤应该不需要五分钟。

## <a name="find-your-function-app"></a>查找函数应用    

1. 登录到 [Azure 门户](https://portal.azure.com/)。 

2. 在门户顶端的搜索栏中，键入函数应用的名称，并从列表中将其选中。

## <a name="create-function"></a>创建 GitHub Webhook 触发的函数

1. 在函数应用中，依次单击“函数”旁边的 **+** 按钮、与所需语言相对应的 **GitHubWebHook** 模板、“创建”。
   
    ![在 Azure 门户中创建 GitHub Webhook 触发的函数。](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. 单击“</> 获取函数 URL”，然后复制并保存相关值。 针对“</> 获取 GitHub 机密”执行相同的操作。 请使用这些值在 GitHub 中配置 Webhook。 

    ![查看函数代码](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
接下来，在 GitHub 存储库中创建 Webhook。 

## <a name="configure-the-webhook"></a>配置 Webhook
1. 在 GitHub 中，导航到自己的存储库。 还可以使用已分叉的任何存储库。
 
2. 依次单击“设置”、“Webhook”和“添加 Webhook”。
   
    ![添加 GitHub webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. 将函数的 URL 和机密粘贴到“有效负载 URL”和“机密”中，然后选择“应用程序/json”作为“内容类型”。

4. 单击“让我选择各个事件”，然后选择“问题注释”并单击“添加 webhook”。
   
    ![设置 webhook URL 和机密](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

现在，Webhook 配置为在添加新问题注释时触发函数。 

## <a name="test-the-function"></a>测试函数
1. 在 GitHub 存储库的新浏览器窗口中，打开“问题”选项卡。

2. 在新窗口中，单击“新建问题”，键入标题，然后单击“提交新问题”。 

2. 在问题中键入注释，然后单击“注释” 。 

3. 在另一 GitHub 窗口中，单击新 Webhook 旁边的“编辑”，向下滚动到“最近交付项”，验证是否已通过函数处理 Webhook 请求。 
 
    ![设置 webhook URL 和机密](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   来自函数的响应应包含 `New GitHub comment: <Your issue comment text>`。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


