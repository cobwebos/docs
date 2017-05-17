---
title: "在 Azure 中构建无服务器社交媒体仪表板 | Microsoft Docs"
description: "在 Azure 中构建无服务器社交媒体仪表板"
services: functions, logic-apps, cognitive-services
keywords: "工作流, 云应用, 云服务, 业务流程, 系统集成, 企业应用程序集成, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>在 Azure 中构建无服务器社交媒体仪表板

[Azure Functions](functions-overview.md) 与 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)集成，可让你使用其他 Azure 服务和第三方服务构建复杂的业务流程。 本主题演示如何从社交媒体源触发逻辑应用，并使用 [Azure 认知服务](../cognitive-services/Welcome.md)分析文本。

本文介绍如何在 Azure 门户中创建一个具有以下功能的逻辑应用：

> [!div class="checklist"]
> * 使用提供的关键字或井号标签检查新的推文。
> * 使用“检测情感”连接器来推测推文中的情感（从差到优）。
> * 使用 Azure 函数将推文情感处理成三个类别（RED、YELLOW 或 GREEN，分别代表差、中、优）。
> * 使用条件来检查情感是否为 RED（差）。
> * 如果条件为 RED，则发送电子邮件。

下图显示了设计器中逻辑应用的一部分：

![逻辑应用设计器中应用的前 2 个步骤的示意图](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>先决条件

* 一个 Azure 帐户。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/) 。
* 一个 Twitter 帐户。

## <a name="create-a-function-app"></a>创建 Function App
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>创建分类函数

完成 Function App 部署后，请打开新的 Function App。 在本部分，你将创建一个函数，用于将推文情感分类为三个类别（RED、YELLOW 或 GREEN，分别代表差、中、优）。

![Function Apps 边栏选项卡，Functions +](media/functions-twitter-email/add_fun.png)

保留默认的“Webhook + API”、“CSharp”，然后选择“创建此函数”。

![Function Apps 边栏选项卡，Functions +](media/functions-twitter-email/add_fun2.png)

现已创建一个 Webhook/API 函数（也称为 HTTP 触发器），所构建的应用可根据需要调用它。 若要创建一个按计划运行的函数，请创建 Timer 函数。

将 *run.csx* 文件的内容替换为以下代码：

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

    if (score < .3)
    {
        category = "RED";
    }
    else if (score < .6)
    {
        category = "YELLOW";
    }

    return req.CreateResponse(HttpStatusCode.OK, category);
}
```

保存更改。

### <a name="test-the-function"></a>测试函数

选择“测试”（位于代码框的右侧）。  在“请求正文”文本框中输入 0.2，然后选择“运行”。 输出显示“RED”，HTTP 状态为 200 OK。

 ![测试 ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>认知服务

创建认知服务帐户。 需要使用认知服务帐户来检测所监视的推文的情感。

导航到“新建”>“智能 + 分析”>“认知服务”。 设置每个必填字段：

![创建认知帐户边栏选项卡](media/functions-twitter-email/cog_svcs_account.png)

| 字段               | 示例值 | 注释 |
| ----------------- | ------------ | ------------- |
| 帐户名 | MyCognitiveServicesAccnt | 输入唯一名称。 |
| API 类型 | 文本分析 API | 选择文本分析 |
| 位置 | 美国西部 | 目前只能选择“美国西部” |
| 定价层 | F0 | 如果已用完调用配额，请设置为更高的层。|
| 资源组 | rg1 | 使用前面指定的资源组。|

### <a name="copy-the-cognitive-services-key"></a>复制认知服务密钥

选择“密钥”。 后面的步骤需要密钥。

 ![密钥](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>创建逻辑应用

在 Azure 门户中，单击“新建”>“Enterprise Integration”>“逻辑应用”

![新建逻辑应用前置步骤](media/functions-twitter-email/new_logicApp.png)

在“创建逻辑应用”边栏选项卡中输入每个字段，然后选择“创建”。

![创建逻辑应用前置步骤](media/functions-twitter-email/new_logicApp2.png)

创建逻辑应用后，它将在设计器中打开。 选择“空白逻辑应用”模板。

![空白逻辑应用](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>将触发器添加到 Twitter

“逻辑应用设计器”显示了可以连接到的许多服务和触发器。

选择“Twitter”服务。

![Twitter 连接器](media/functions-twitter-email/twitter_connector.png)

选择“发布新推文时”触发器。

![“发布新推文时”触发器](media/functions-twitter-email/tw_trig.png)

登录到你的 Twitter 帐户。

![登录到你的 Twitter 帐户](media/functions-twitter-email/signin_twit.png)

输入密码并选择“为应用授权”。

![完成上面的步骤后，在新窗口中进行 Twitter 身份验证](media/functions-twitter-email/auth_twit.png)

输入搜索文本、频率和间隔。 如果指定热门的井号标签（例如 #football、#soccer 或 #futbol），则可以在认知服务帐户中快速使用所有已分配的服务调用。 如果已用完调用配额，可以提高定价层。 

每隔 15 分钟搜索 #Azure 一次：

![每隔 15 分钟搜索 #Azure 一次](media/functions-twitter-email/azure_tweet.png)

保存应用。

### <a name="add-a-text-analytics-connector"></a>添加“文本分析”连接器

文本分析连接器可检测推文情感。

选择“新建步骤”，然后选择“添加操作”。

![选择“新建步骤”，然后选择“添加操作”](media/functions-twitter-email/new_step.png)

添加“文本分析”连接器。

![选择操作窗口](media/functions-twitter-email/choose_action.png)

选择“检测情感”操作。 情感评分通常比较准确，但有时会错误地解释文本。

![检测情感](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>创建“检测情感”操作

  * 输入连接名称，例如 **MyKey**。
  * 复制并粘贴在[创建认知服务帐户](#cognitive-services)步骤中创建的密钥。
  * 选择“创建” 。
  * 保存应用。

![检测情感](media/functions-twitter-email/ta_detect_sent.png)

选择“要分析的文本”旁边的“推文文本”图标。

![检测情感](media/functions-twitter-email/ds_tta.png)

![检测情感](media/functions-twitter-email/ds_tta2.png)

保存应用。

## <a name="connect-to-the-azure-function"></a>连接到 Azure Function

在本部分，你将添加前面创建的、已将推文情感分类为 RED、YELLOW 或 GREEN 的函数。

* 在逻辑应用设计器中选择“新建步骤”，然后选择“添加操作”。
* 选择“Azure Functions”。
* 选择“选择 Azure 函数”。

![显示“选择 Azure 函数”的 Azure Functions 框](media/functions-twitter-email/choose_fun.png)

* 选择前面创建的 Azure 函数。
* 选择“评分”以填充“请求正文”。

![得分](media/functions-twitter-email/trigger_score.png)

保存应用。

## <a name="add-email-notification"></a>添加电子邮件通知

在本部分，我们将针对包含消极情感的推文（RED 条件）添加条件检查。

* 选择“新建步骤”。
* 选择“添加条件”。
* 在第一个“选择值”文本框中选择“正文”。
* 在第二个“选择值”文本框中输入“RED”。
* 保存应用。

![条件框](media/functions-twitter-email/condition.png)

* 在“如果是，则不执行任何操作”框中，选择“添加操作”。
* 在“搜索所有服务和操作”框中输入 Outlook 或 Gmail。 本教程使用 Outlook。 有关 Gmail 的说明，请参阅 [添加 Gmail 操作] (../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger)。 注意：如果你已注册个人 [Microsoft 帐户](https://account.microsoft.com/account)，可以使用该帐户代替 Outlook.com 帐户。

![选择操作的框](media/functions-twitter-email/outlook.png)

选择“使用 Outlook.com 发送电子邮件”。

![Outlook.com 框](media/functions-twitter-email/sendEmail.png)

登录到 Outlook.com。

![登录框](media/functions-twitter-email/signin_outlook.png)

输入以下项：

   * **收件人**：应该将邮件发送到的电子邮件地址。
   * **主题**：评分。
   * **正文**：位置和推文文本。

![发送电子邮件的框](media/functions-twitter-email/sendEmail2.png)

保存应用。
选择“运行”启动应用。

### <a name="check-the-status"></a>检查状态

在逻辑应用边栏选项卡中选择“概述”，然后在“运行历史记录”列中选择一行：

![概述边栏选项卡](media/functions-twitter-email/over1.png)

下图显示了条件不为 true，因此未发送电子邮件时的运行详细信息。

![概述边栏选项卡](media/functions-twitter-email/skipped.png)

如果想要立即测试“发送电子邮件”函数，请执行以下操作：

* 将第一个步骤（“发布新推文时”）中的“输入”更改为热门字词，例如 #football、#soccer 或 #futbol。

处理热门字词所消耗的资源比处理较不热门字词要多。 在验证电子邮件是否正常工作后，可以更改搜索词。

下图显示了条件为 true，因此已发送电子邮件时的运行详细信息。

![概述边栏选项卡](media/functions-twitter-email/sent.png)

可以选择任一服务框，显示有关用于运行的数据的详细信息。 选择“发布新推文时”显示搜索文本和所有输出，甚至包括未使用的输出。

## <a name="next-steps"></a>后续步骤

*  [Azure Functions 简介](functions-overview.md)
*  [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [添加条件和运行工作流](../logic-apps/logic-apps-use-logic-app-features.md)
*  [逻辑应用模板](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [通过 Azure Resource Manager 模板创建逻辑应用](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

