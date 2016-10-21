<properties
   pageTitle="创建 Webhook 或 API Azure 函数 | Microsoft Azure"
   description="使用 Azure Functions 创建由 WebHook 或 API 调用调用的函数。"
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/30/2016"
   ms.author="glenga"/>
   
# 创建 Webhook 或 API Azure 函数

Azure Functions 提供事件驱动、按需计算体验，让你能够创建以各种编程语言实现的计划或触发代码单元。若要了解有关 Azure Functions 的详细信息，请参阅 [Azure Functions 概述](functions-overview.md)。

本主题演示如何创建由 GitHub Webhook 调用的新 Node.js 函数。新函数基于 Azure Functions 门户中的预定义模板创建。还可观看简短视频，了解如何在门户中执行这些步骤。

## 观看视频

以下视频演示如何执行本教程中的基本步骤

[AZURE.VIDEO create-a-web-hook-or-api-azure-function]

##通过模板创建 Webhook 触发函数

Function App 在 Azure 中托管函数的执行。必须先具有有效的 Azure 帐户，然后才能创建函数。如果还没有 Azure 帐户，[可以使用免费帐户](https://azure.microsoft.com/free/)。

1. 转到 [Azure Functions 门户](https://functions.azure.com/signin)，然后使用你的 Azure 帐户登录。

2. 如果存在要使用的现有 Function App，请从“你的 Function App”中选择它，然后单击“打开”。若要创建新的 Function App，请键入新 Function App 的唯一“名称”或接受生成的名称，选择首选“区域”，然后单击“创建 + 开始使用”。

3. 在 Function App 中，单击“+ 新建函数”>“GitHub Webhook - 节点”>“创建”。此操作基于指定模板创建具有默认名称的函数。

	![创建新的 GitHub Webhook 函数](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png)

4. 在“开发”选项卡中，注意“代码”窗口中的示例 express.js 函数。此函数从问题注释 Webhook 接收 GitHub 请求、记录问题文本并将响应作为 `New GitHub comment: <Your issue comment text>` 发送到 Webhook。


	![创建新的 GitHub Webhook 函数](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png)

5. 复制“函数 URL”和“GitHub 密码”值。在 GitHub 中创建 Webhook 时，将需要这些值。

6. 向下滚动到“运行”，注意请求正文中问题注释的预定义 JSON 正文，然后单击“运行”。
 
	只要提供任意预期正文 JSON 数据，然后单击“运行”按钮，就可以在“开发”选项卡中直接测试基于模板的新函数。在这种情况下，模板具有问题注释的预定义正文。
 
接下来，将在 GitHub 存储库中实际创建 Webhook。

##配置 Webhook

1. 在 GitHub 中，导航到拥有的存储库；这包括派生的所有存储库。
 
2. 单击“设置”>“Webhook 和服务”>“添加 Webhook”。

	![创建新的 GitHub Webhook 函数](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)

3. 将函数的 URL 和密码粘贴到“有效负载 URL”和“密码”，然后单击“让我选择每个事件”，选择“问题注释”，然后单击“添加 Webhook”。

	![创建新的 GitHub Webhook 函数](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png)

此时，GitHub Webhook 配置为在添加新问题注释时触发函数。现在，请对它进行测试。

##测试函数

1. 在 GitHub 存储库的新浏览器窗口中打开“问题”选项卡，单击“新建问题”，键入标题，然后单击“提交新问题”。也可以打开现有问题。

2. 在问题中键入注释，然后单击“注释”。此时，可以回到 GitHub 中的新 Webhook，在“最近传递”下可以看到已发送的 Webhook 请求，且响应的正文是 `New GitHub comment: <Your issue comment text>`。

3. 回到 Functions 门户，向下滚动到日志，可以看到已触发的函数，且值 `New GitHub comment: <Your issue comment text>` 已写入流式处理日志。


##后续步骤

请参阅以下主题，获取有关 Azure Functions 的详细信息。

+ [Azure Functions 开发人员参考](functions-reference.md)，用于编码函数的程序员参考。
+ [测试 Azure Functions](functions-test-a-function.md)，介绍可用于测试函数的各种工具和技巧。
+ [如何缩放 Azure Functions](functions-scale.md)，讨论 Azure Functions 提供的服务计划（包括动态服务计划）以及如何选择合适的计划。


[AZURE.INCLUDE [入门笔记](../../includes/functions-get-help.md)]

<!---HONumber=AcomDC_0921_2016-->