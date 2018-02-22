---
title: "创建与 Azure 逻辑应用集成的函数 | Microsoft Docs"
description: "创建一个与 Azure 逻辑应用和 Azure 认知服务集成的函数，对推文情绪进行分类，并在情绪较差时发送通知。"
services: functions, logic-apps, cognitive-services
keywords: "工作流, 云应用, 云服务, 业务流程, 系统集成, 企业应用程序集成, EAI"
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9e9369d9dc9f7298b93927b49685f4e24de8a7fd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>创建与 Azure 逻辑应用集成的函数

Azure Functions 在逻辑应用设计器中与 Azure 逻辑应用集成。 借助这种集成，可以利用 Functions 的计算能力来与其他 Azure 服务和第三方服务进行协调。 

本教程介绍如何在 Azure 上使用 Functions、逻辑应用和 Microsoft 认知服务来分析 Twitter 文章中的情感。 HTTP 触发的函数根据情感评分将推文分类为绿色、黄色或红色。 检测到较差的情感时，会发送一封电子邮件。 

![逻辑应用设计器中应用的前两个步骤的示意图](media/functions-twitter-email/designer1.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建认知服务 API 资源。
> * 创建一个用来对推文情感进行分类的函数。
> * 创建连接到 Twitter 的逻辑应用。
> * 将情感检测功能添加到逻辑应用。 
> * 将逻辑应用连接到函数。
> * 根据函数的响应发送电子邮件。

## <a name="prerequisites"></a>先决条件

+ 有效的 [Twitter](https://twitter.com/) 帐户。 
+ [Outlook.com](https://outlook.com/) 帐户（用于发送通知）。
+ 本主题以在[从 Azure 门户创建第一个函数](functions-create-first-azure-function.md)主题中创建的资源为基础。  
现在请完成以下步骤创建 Function App（如果尚未这样做）。

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

Azure 中以单个资源的形式提供了认知服务 API。 使用文本分析 API 可检测受监视的推文观点。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 在 Azure 门户的左上角单击“创建资源”。

3. 单击“AI + 分析” > “文本分析 API”。 然后，请使用表中指定的设置，接受条款，并选中“固定到仪表板”。

    ![创建认知资源页](media/functions-twitter-email/cog_svcs_resource.png)

    | 设置      |  建议的值   | 说明                                        |
    | --- | --- | --- |
    | **Name** | MyCognitiveServicesAccnt | 选择唯一的帐户名称。 |
    | **位置** | 美国西部 | 使用最靠近的位置。 |
    | **定价层** | F0 | 从最低的层着手。 如果已用完调用配额，请扩展到更高的层。|
    | **资源组** | myResourceGroup | 请本教程中的所有服务使用同一个资源组。|

4. 单击“创建”以创建资源。 创建资源后，请选择已固定到仪表板的新认知服务资源。 

5. 在左侧导航栏中，单击“密钥”，复制并保存“密钥 1”的值。 使用此密钥将逻辑应用连接到认知服务 API。 
 
    ![密钥](media/functions-twitter-email/keys.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-the-function-app"></a>创建函数应用

函数可让你方便地卸载逻辑应用工作流中的处理任务。 本教程使用 HTTP 触发的函数来处理认知服务提供的推文情感评分，并返回一个类别值。  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>创建 HTTP 触发的函数  

1. 展开 Function App，单击“Functions”旁边的 + 按钮。 如果这是 Function App 中的第一个函数，请选择“自定义函数”。 此时将显示函数模板的完整集合。

    ![Azure 门户中的 Functions 快速入门页](media/functions-twitter-email/add-first-function.png)

2. 在搜索字段中，键入 `http`，然后针对 HTTP 触发器模板选择“C#”。 

    ![选择 HTTP 触发器](./media/functions-twitter-email/select-http-trigger-portal.png)

3. 为函数键入**名称**，选择 `Function` 作为**[身份验证级别](functions-bindings-http-webhook.md#http-auth)**，然后选择“创建”。 

    ![创建 HTTP 触发的函数](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    这将使用 HTTP 触发器模板创建 C# 脚本函数。 代码将作为 `run.csx` 显示在新窗口中。

4. 将 `run.csx` 文件的内容替换为以下代码，然后单击“保存”：

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
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
    此函数代码基于请求中收到的情感评分返回颜色类别。 

4. 若要测试该函数，请单击最右边的“测试”展开“测试”选项卡。在“请求正文”中键入 `0.2` 的值，并单击“运行”。 响应正文中将返回 **RED** 值。 

    ![在 Azure 门户中测试函数](./media/functions-twitter-email/test.png)

现已创建一个可对情感评分分类的函数。 接下来，请创建用于将函数与 Twitter 和认知服务 API 集成的逻辑应用。 

## <a name="create-a-logic-app"></a>创建逻辑应用   

1. 在 Azure 门户中，单击左上角的“新建”按钮。

2. 单击“Enterprise Integration” > “逻辑应用”。 然后，请使用表中指定的设置，选中“固定到仪表板”，单击“创建”。
 
4. 然后，请键入“名称”（例如 `TweetSentiment`），使用表中指定的设置，接受条款，并选中“固定到仪表板”。

    ![在 Azure 门户创建逻辑应用](./media/functions-twitter-email/new_logic_app.png)

    | 设置      |  建议的值   | 说明                                        |
    | ----------------- | ------------ | ------------- |
    | **Name** | TweetSentiment | 选择适当的应用名称。 |
    | **资源组** | myResourceGroup | 选择与前面相同的现有资源组。 |
    | **位置** | 美国东部 | 选择靠近你的位置。 |    

4. 选中“固定到仪表板”，然后单击“创建”创建逻辑应用。 

5. 创建应用后，请单击已固定到仪表板的新逻辑应用。 然后，在逻辑应用设计器中向下滚动并单击“空白逻辑应用”模板。 

    ![“空白逻辑应用”模板](media/functions-twitter-email/blank.png)

现在，可以使用逻辑应用设计器将服务和触发器添加到应用。

## <a name="connect-to-twitter"></a>连接到 Twitter

首先，请与 Twitter 帐户建立连接。 逻辑应用将轮询推文，这会触发应用运行。

1. 在设计器中单击“Twitter”服务，并单击“发布新推文时”触发器。 登录到 Twitter 帐户，并授权逻辑应用使用你的帐户。

2. 使用表中指定的 Twitter 触发器设置。 

    ![Twitter 连接器设置](media/functions-twitter-email/azure_tweet.png)

    | 设置      |  建议的值   | 说明                                        |
    | ----------------- | ------------ | ------------- |
    | **搜索文本** | #Azure | 使用足够热门的井号标签按所选的间隔生成新的推文。 如果使用免费层并且井号标签过于热门，可能很快就会用完认知服务 API 中的事务配额。 |
    | **频率** | 分钟 | 用于轮询 Twitter 的频率单位。  |
    | **间隔** | 15 | 每两次处理 Twitter 请求所用的时间，采用频率单位。 |

3.  单击“保存”连接到 Twitter 帐户。 

应用现已连接到 Twitter。 接下来，请连接到文本分析以检测收集到的推文的情感。

## <a name="add-sentiment-detection"></a>添加情感检测

1. 单击“新建步骤”，并单击“添加操作”。

    ![选择“新建步骤”，并选择“添加操作”](media/functions-twitter-email/new_step.png)

2. 在“选择操作”中单击“文本分析”，并单击“检测情感”操作。

    ![检测情感](media/functions-twitter-email/detect_sent.png)

3. 键入连接名称（例如 `MyCognitiveServicesConnection`），粘贴保存的认知服务 API 密钥，并单击“创建”。  

4. 单击“要分析的文本” > “推文文本”，并单击“保存”。  

    ![检测情感](media/functions-twitter-email/ds_tta.png)

配置情感检测后，可将一个连接添加到使用情感评分输出的函数。

## <a name="connect-sentiment-output-to-your-function"></a>将情感输出连接到函数

1. 在逻辑应用设计器中单击“新建步骤” > “添加操作”，并单击“Azure Functions”。 

2. 单击“选择 Azure 函数”，选择前面创建的 **CategorizeSentiment** 函数。  

    ![显示“选择 Azure 函数”的 Azure Functions 框](media/functions-twitter-email/choose_fun.png)

3. 在“请求正文”中单击“评分”，并单击“保存”。

    ![得分](media/functions-twitter-email/trigger_score.png)

现在，在从逻辑应用发送情感评分时，将触发该函数。 该函数会将一个带有颜色编码的类别返回到逻辑应用。 接下来，请添加当函数返回 **RED** 情感值时要发送的电子邮件通知。 

## <a name="add-email-notifications"></a>添加电子邮件通知

工作流的最后一部分是当情感评分为 _RED_ 时触发电子邮件。 本主题使用 Outlook.com 连接器。 可以执行类似的步骤来使用 Gmail 或 Office 365 Outlook 连接器。   

1. 在逻辑应用设计器中，单击“新建步骤” > “添加条件”。 

2. 单击“选择值”，并单击“正文”。 选择“等于”，单击“选择值”并键入 `RED`，然后单击“保存”。 

    ![将条件添加到逻辑应用。](media/functions-twitter-email/condition.png)

3. 在“如果是”中单击“添加操作”，搜索 `outlook.com`，单击“发送电子邮件”，并登录到 Outlook.com 帐户。
    
    ![为条件选择操作。](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > 如果没有 Outlook.com 帐户，可以选择另一个连接器，例如 Gmail 或 Office 365 Outlook

4. 在“发送电子邮件”操作中，请使用表中指定的电子邮件设置。 

    ![为“发送电子邮件”操作配置电子邮件。](media/functions-twitter-email/send_email.png)

    | 设置      |  建议的值   | 说明  |
    | ----------------- | ------------ | ------------- |
    | **To** | 键入电子邮件地址 | 接收通知的电子邮件地址。 |
    | **主题** | 检测到消极的推文情感  | 电子邮件通知的主题行。  |
    | **正文** | 推文文本、位置 | 单击“推文文本”和“位置”参数。 |

5.  单击“ **保存**”。

完成工作流后，可以启用逻辑应用并查看函数的工作状态。

## <a name="test-the-workflow"></a>测试工作流

1. 在逻辑应用设计器中，单击“运行”启动应用。

2. 在左列中，单击“概述”查看逻辑应用的状态。 
 
    ![逻辑应用执行状态](media/functions-twitter-email/over1.png)

3. （可选）单击某个运行查看执行详细信息。

4. 转到函数，查看日志，并验证是否已收到并处理情感值。
 
    ![查看函数日志](media/functions-twitter-email/sent.png)

5. 检测到潜在的消极情感时，将收到一封电子邮件。 如果未收到电子邮件，可以更改函数代码，以便每次都返回 RED：

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    验证电子邮件通知后，请改回原始代码：

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > 完成本教程后，应禁用该逻辑应用。 禁用该应用可以避免产生执行费用，以及用完认知服务 API 中的事务配额。

我们已看到，将 Functions 集成到逻辑应用工作流的过程是多么简单。

## <a name="disable-the-logic-app"></a>禁用逻辑应用

要禁用逻辑应用，请单击“概述”，并单击屏幕顶部的“禁用”。 这可以停止逻辑应用并避免产生费用，同时又不必删除该应用。 

![函数日志](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建认知服务 API 资源。
> * 创建一个用来对推文情感进行分类的函数。
> * 创建连接到 Twitter 的逻辑应用。
> * 将情感检测功能添加到逻辑应用。 
> * 将逻辑应用连接到函数。
> * 根据函数的响应发送电子邮件。

请继续学习下一教程，了解如何为函数创建无服务器 API。

> [!div class="nextstepaction"] 
> [使用 Azure Functions 创建无服务器 API](functions-create-serverless-api.md)

若要详细了解逻辑应用，请参阅 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

