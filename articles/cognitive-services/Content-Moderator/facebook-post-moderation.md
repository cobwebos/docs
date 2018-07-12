---
title: 使用 Azue 内容审查器执行 Facebook 内容审核 | Microsoft Docs
description: 使用基于机器学习的内容审查器审查 Facebook 页面
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365556"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>使用内容审查器执行 Facebook 内容审核

本教程介绍如何使用基于机器学习的内容审查器帮助审查 Facebook 帖子和评论。

教程将指导完成以下步骤：

1. 创建内容审查器团队。
2. 创建 Azure Functions，用于侦听来自内容审查器和 Facebook 的 HTTP 事件。
3. 创建 Facebook 页面和应用，并将其连接到内容审查器。

完成操作后，Facebook 将向内容审查器发送访问者发布的内容。 根据匹配阈值，内容审查器工作流将发布内容，或在评审工具中创建评审。 

下图显示了解决方案的构建基块。

![Facebook 帖子审查](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>创建内容审查器团队

请参阅[快速入门](quick-start.md)页面，以注册内容审查器和创建团队。

## <a name="configure-image-moderation-workflow-threshold"></a>配置图像审查工作流（阈值）

请参阅[工作流](review-tool-user-guide/workflows.md)页面，以配置自定义图像工作流（阈值）。 注意工作流名称。

## <a name="3-configure-text-moderation-workflow-threshold"></a>3.配置文本审查工作流（阈值）

使用[工作流](review-tool-user-guide/workflows.md)页面上的类似步骤，配置自定义文本阈值和工作流。 注意工作流名称。

![配置文本工作流](images/text-workflow-configure.PNG)

使用“执行工作流”按钮测试工作流。

![测试文本工作流](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>创建 Azure Functions

登录 [Azure 管理门户](https://portal.azure.com/)，以创建 Azure Functions。 执行以下步骤:

1. 按照 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 页面所示，创建 Azure Function App。
2. 打开新创建的 Function App。
3. 在 App 中，导航到“平台功能”->“应用程序设置”
4. 定义以下[应用程序设置](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)：

> [!NOTE]
> cm: Region 应为区域的名称（不含空格）。
> 例如，应为 westeurope，而不是 West Europe；应为 westcentralus，而不是 West Central US，以此类推。
>

| 应用设置 | 说明   | 
| -------------------- |-------------|
| cm:TeamId   | 内容审查器 TeamId  | 
| cm:SubscriptionKey | 内容审查器的订阅密钥 - 请参阅[凭据](/review-tool-user-guide/credentials.md) | 
| cm:Region | 内容审查器的区域名称，不含空格。 请参阅前面的说明。 |
| cm:ImageWorkflow | 对图像运行的工作流的名称 |
| cm:TextWorkflow | 对文本运行的工作流的名称 |
| cm:CallbackEndpoint | 在本指南后面部分创建的 CMListener Function App 的 URL |
| fb:VerificationToken | 机密令牌，还用于订阅 Facebook 源事件 |
| fb:PageAccessToken | Facebook 图形 API 访问令牌不会过期，并允许函数代表你隐藏/删除帖子。 |

5. 创建名为 FBListener 的新 HttpTrigger-CSharp 函数。 此函数接收来自 Facebook 的事件。 通过执行以下步骤创建此函数：

    1. 保持 [Azure Functions 创建](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal)页处于打开状态，以供参考。
    2. 单击“+”创建新函数。
    3. 选择“开始使用自己的/自定义函数”选项，而非内置模板。
    4. 单击显示为 HttpTrigger-CSharp 的磁贴。
    5. 输入名称 FBListener。 “授权级别”字段应设置为“函数”。
    6. 单击“创建”。
    7. 将 run.csx 的内容替换为 [FbListener/run.csx](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx) 的内容。

6. 创建名为 CMListener 的新 HttpTrigger-CSharp 函数。 此函数接收来自 Facebook 的事件。 按照以下步骤创建此函数。

    1. 保持 [Azure Functions 创建](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal)页处于打开状态，以供参考。
    2. 单击“+”创建新函数。
    3. 选择“开始使用自己的/自定义函数”选项，而非内置模板。
    4. 单击显示 HttpTrigger-CSharp 的磁贴
    5. 输入名称 CMListener。 “授权级别”字段应设置为“函数”。
    6. 单击“创建”。
    7. 将 run.csx 的内容替换为 [CMListener/run.csx](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx) 的内容。

## <a name="configure-the-facebook-page-and-app"></a>配置 Facebook 页面和应用
1. 创建 Facebook 应用。

    1. 导航到 [Facebook 开发人员网站](https://developers.facebook.com/)
    2. 单击“我的应用”。
    3. 添加新应用。
    4. 选择“Webhook”->“开始”
    5. 选择“页面”->“订阅此主题”
    6. 提供 FBListener URL 作为回叫 URL，并在“Function App 设置”下提供配置的“验证令牌”
    7. 订阅后，向下滚动到源，然后选择“订阅”。

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

就这么简单！

解决方案将向内容审查器发送 Facebook 页上发布的所有图像和文本。 将调用之前配置的工作流。 未通过工作流中所定义条件的内容将进入评审工具内接受评审。 其余内容将发布。

## <a name="license"></a>许可

所有 Microsoft 认知服务 SDK 和示例均通过 MIT 许可进行许可。 有关详细信息，请参阅[许可](https://microsoft.mit-license.org/)。

## <a name="developer-code-of-conduct"></a>开发人员行为准则

使用认知服务（包括此客户端库和示例）的开发人员应遵循“面向 Microsoft 认知服务的开发人员行为准则”（参见 http://go.microsoft.com/fwlink/?LinkId=698895）。

## <a name="next-steps"></a>后续步骤

1. 观看 Microsoft Build 2017 中此解决方案的[演示（视频）](https://channel9.msdn.com/Events/Build/2017/T6033)。
1. [GitHub 上的 Facebook 示例](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
