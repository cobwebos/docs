---
title: 教程：审查 Facebook 内容 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 在本教程中，你将了解如何使用基于机器学习的内容审查器帮助审查 Facebook 帖子和评论。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: pafarley
ms.openlocfilehash: 662eca2a727f3112f169ab8d669bf18c81700275
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871022"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>教程：使用 Azure 内容审查器审查 Facebook 帖子和评论

本教程介绍如何使用 Azure 内容审查器帮助审查 Facebook 页面上的帖子和评论。 Facebook 会将访客发布的内容发送到内容审查器服务。 然后，内容审查器工作流会根据内容评分和阈值发布内容，或者在评审工具中创建评审。 有关此方案的可行示例，请参阅[版本 2017 演示视频](https://channel9.msdn.com/Events/Build/2017/T6033)。

本教程演示如何：

> [!div class="checklist"]
> * 创建内容审查器团队。
> * 创建 Azure Functions，用于侦听来自内容审查器和 Facebook 的 HTTP 事件。
> * 使用 Facebook 应用程序将 Facebook 页面链接到内容审查器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

此图演示了此方案的每个组件：

![通过“FBListener”从 Facebook 接收信息，并通过“CMListener”发送信息的内容审查器示意图](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>先决条件

- 内容审查器的订阅密钥。 遵照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅内容审查器服务并获取密钥。
- 一个 [Facebook 帐户](https://www.facebook.com/)。

## <a name="create-a-review-team"></a>创建评审团队

请参阅[在 Web 上试用内容审查器](quick-start.md)快速入门，获取有关如何注册[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)和创建评审团队的说明。 记下“凭据”页上的“团队 ID”值。

## <a name="configure-image-moderation-workflow"></a>配置图像审查工作流

请参阅[定义、测试和使用工作流](review-tool-user-guide/workflows.md)指南，以创建自定义图像工作流。 这样，内容审查器便可以自动检查 Facebook 上的图像，并将其中的某些图像发送到评审工具。 记下工作流的**名称**。

## <a name="configure-text-moderation-workflow"></a>配置文本审查工作流

同样请参阅[定义、测试和使用工作流](review-tool-user-guide/workflows.md)指南；这一次需创建自定义文本工作流。 这样，内容审查器便可以自动检查文本内容。 记下工作流的**名称**。

![配置文本工作流](images/text-workflow-configure.PNG)

使用“执行工作流”按钮测试工作流。

![测试文本工作流](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>创建 Azure Functions

登录到 [Azure 门户](https://portal.azure.com/)并执行以下步骤：

1. 按照 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 页面所示，创建 Azure Function App。
2. 打开新建的函数应用。
3. 在该应用中，转到“平台功能”选项卡并选择“应用程序设置”。 在下一页的“应用程序设置”部分，滚动到列表底部并单击“添加新设置”。 添加以下键/值对
    
    | 应用设置名称 | 值   | 
    | -------------------- |-------------|
    | cm:TeamId   | 内容审查器 TeamId  | 
    | cm:SubscriptionKey | 内容审查器的订阅密钥 - 请参阅[凭据](review-tool-user-guide/credentials.md) | 
    | cm:Region | 内容审查器的区域名称，不含空格。 请参阅前面的说明。 |
    | cm:ImageWorkflow | 对图像运行的工作流的名称 |
    | cm:TextWorkflow | 对文本运行的工作流的名称 |
    | cm:CallbackEndpoint | 在本指南后面部分创建的 CMListener Function App 的 URL |
    | fb:VerificationToken | 机密令牌，还用于订阅 Facebook 源事件 |
    | fb:PageAccessToken | Facebook 图形 API 访问令牌不会过期，并允许函数代表你隐藏/删除帖子。 |

    单击页面顶部的“保存”按钮。

1. 使用左窗格中的 **+** 按钮打开“新建函数”窗格。

    ![Azure Functions 窗格，其中已突出显示“添加函数”按钮。](images/new-function.png)

    然后单击页面顶部的“+ 新建函数”。 此函数接收来自 Facebook 的事件。 通过执行以下步骤创建此函数：

    1. 单击显示了“HTTP 触发器”的磁贴。
    1. 输入名称 FBListener。 “授权级别”字段应设置为“函数”。
    1. 单击“创建”。
    1. 将 **run.csx** 的内容替换为 **FbListener/run.csx** 中的内容。

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-160)]

1. 创建名为 **CMListener** 的新“HTTP 触发器”函数。 此函数接收来自内容审查器的事件。 将 **run.csx** 的内容替换为 **CMListener/run.csx** 中的内容。

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-106)]

---

## <a name="configure-the-facebook-page-and-app"></a>配置 Facebook 页面和应用
1. 创建 Facebook 应用。

    ![Facebook 开发人员页面](images/facebook-developer-app.png)

    1. 导航到 [Facebook 开发人员网站](https://developers.facebook.com/)
    2. 单击“我的应用”。
    3. 添加新应用。
    1. 为应用命名
    1. 选择“Webhook”->“设置”
    1. 在下拉菜单中选择“页面”，然后选择“订阅此对象”
    1. 提供 FBListener URL 作为回叫 URL，并在“Function App 设置”下提供配置的“验证令牌”
    1. 订阅后，向下滚动到源，然后选择“订阅”。

2. 创建 Facebook 页面。

    1. 导航到 [Facebook](https://www.facebook.com/bookmarks/pages) 并创建新的 Facebook 页。
    2. 执行以下步骤以允许 Facebook 应用访问此页面：
        1. 导航到[图形 API 资源管理器](https://developers.facebook.com/tools/explorer/)。
        2. 选择“应用程序”。
        3. 选择“页面访问令牌”，发送 Get 请求。
        4. 单击响应中的“页面 ID”。
        5. 现在，将 /subscribed_apps 追加到 URL 并发送 Get（空响应）请求。
        6. 提交 Post 请求。 获取 success: true 形式的响应。

3. 创建不会过期的图形 API 访问令牌。

    1. 导航到[图形 API 资源管理器](https://developers.facebook.com/tools/explorer/)。
    2. 选择“应用程序”选项。
    3. 选择“获取用户访问令牌”选项。
    4. 在“选择权限”下，选择“manage_pages”和“publish_pages”选项。
    5. 我们将在下一步使用访问令牌（短期令牌）。

4. 下面几个步骤将使用 Postman。

    1. 打开 Postman（或在[此处](https://www.getpostman.com/)获取）。
    2. 导入以下两个文件：
        1. [Postman 集合](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 环境](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 更新以下环境变量：
    
        | 密钥 | 值   | 
        | -------------------- |-------------|
        | appId   | 在此处插入你的 Facebook 应用标识符  | 
        | appSecret | 在此处插入 Facebook 应用的机密 | 
        | short_lived_token | 插入在上一步中生成的短期用户访问令牌 |
    4. 现在，运行集合中列出的 3 个 API： 
        1. 选择“生成长期访问令牌”并单击“发送”。
        2. 选择“获取用户 ID”并单击“发送”。
        3. 选择“获取永久页面访问令牌”并单击“发送”。
    5. 复制响应中的 access_token 值并将其分配给应用设置，fb:PageAccessToken。

解决方案将向内容审查器发送 Facebook 页上发布的所有图像和文本。 然后会调用前面配置的工作流。 不符合工作流中所定义条件的内容将传入评审工具进行评审。 剩余的内容会自动发布。

## <a name="next-steps"></a>后续步骤

在本教程中，你已设置一个程序用于分析产品图像，以便按产品类型对其进行标记，并使评审团队能够在内容审查方面做出明智的决策。 接下来，请了解有关图像审查的详细信息。

> [!div class="nextstepaction"]
> [图像审查](./image-moderation-api.md)
