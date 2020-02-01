---
title: 教程：在 QnA Maker 中创建、发布和回答问题
description: 本教程说明如何使用基于公共 Web 的常见问题解答中的问题和解答来创建新知识库。 保存、训练和发布知识库。 发布知识库后，使用 cURL 命令发送问题并接收解答。 然后创建机器人，并使用相同的问题测试机器人。
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844130"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>教程：在 QnA Maker 门户中创建知识库

通过基于 Web 的公共 FAQ 创建包含问答的新知识库。 保存、训练和发布知识库。 发布知识库后，使用 cURL 命令发送问题并接收解答。 然后创建机器人，并使用相同的问题测试机器人。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 QnA Maker 门户中创建知识库。
> * 查看、保存和训练知识库。
> * 发布知识库。
> * 使用 cURL 查询知识库。
> * 创建机器人。


> [!NOTE]
> [“Azure-Samples/cognitive-services-qnamaker-csharp”GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)中提供本教程的编程版本和完整的解决方案  。

## <a name="prerequisites"></a>必备条件

本教程需要现有的 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。

## <a name="create-a-knowledge-base"></a>创建知识库

1. 登录 [QnA Maker](https://www.qnamaker.ai) 门户。

1. 从顶部菜单中选择“创建知识库”。 

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. 请跳过第一步，因为你将使用现有的 QnA Maker 服务。

1. 选择现有设置：

    |设置|目的|
    |--|--|
    |Microsoft Azure Directory ID|此 ID 与用于登录 Azure 门户和 QnA Maker 门户的帐户相关联。 |
    |Azure 订阅名称|在其中创建了 QnA Maker 资源的计费帐户。|
    |Azure QnA 服务|现有的 QnA Maker 资源。|
    |语言|用于服务中的所有知识库的语言。 不要在单个资源中混合使用语言。 这会损害应答质量。|

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. 输入知识库名称 `My Tutorial kb`。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. 为知识库填充以下设置：

    |设置名称|设置值|目的|
    |--|--|--|
    |启用多轮提取|已选中|允许后续提示。|
    |默认答案文本|`No good answer was found.`|与问题不匹配时返回。|
    |代码|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |该 URL 中常见问题解答内容的格式为一个问题接一个答案。 QnA Maker 可以通过解释此格式来提取问题和相关联的答案。|
    |文件 | 不在本教程中使用|此项上传用于问题和答案的文件。 |
    |聊天个性化内容|友好|此项为常见问题和解答提供友好且随和的 [个性](../Concepts/best-practices.md#chit-chat) 化内容。 可以稍后编辑这些问题和解答。 |

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. 选择“创建 KB”，完成创建过程。 

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>查看、保存和训练知识库

1. 查看问题和解答。 第一页是 URL 中的问题和解答。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. 从表的底部选择最后一页的问题和解答。 此页面显示来自聊天个性化内容的问题和解答。

1. 在问题和解答列表上方的工具栏中，选择“查看选项”图标，然后选择“显示元数据”   。 此时会显示每个问题和解答的元数据标记。 聊天问题已设置“编辑: 聊天”元数据。  此元数据会与所选答案一起返回到客户端应用程序。 客户端应用程序（例如聊天机器人）可以使用此筛选过的元数据来确定其他处理或与用户之间的交互。

    ![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. 在顶部菜单栏中选择“保存并训练”  。

## <a name="publish-to-get-knowledge-base-endpoints"></a>通过发布获取知识库终结点

从顶部菜单中选择“发布”按钮。  在发布页上选择“发布”  。

![QnA Maker 门户的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

发布知识库以后，会显示终结点。

![终结点设置的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

请勿关闭此“发布”页。  稍后在本教程中需要用它来创建机器人。

## <a name="use-curl-to-query-for-an-faq-answer"></a>使用 cURL 查询 FAQ 解答

1. 选择“Curl”选项卡。 

    ![“cURL”选项卡的屏幕截图](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. 复制“cURL”选项卡的文本，在支持 cURL 的终端或命令行中运行它。  授权标头的值包含文本 `Endpoint` 和一个尾随空格以及密钥。

1. 将 `<Your question>` 替换为 `How large can my KB be?`。 这与问题 `How large a knowledge base can I create?` 接近，但并不完全相同。 QnA Maker 会应用自然语言处理来确定两个问题是否相同。

1. 运行 cURL 命令，然后接收包含评分和解答的 JSON 响应。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }

    ```

    分数为 42.81%，表明 QnA Maker 不怎么确信其答案。

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>使用 cURL 查询聊天式解答

1. 在支持 cURL 的终端中，将 `How large can my KB be?` 替换为用户进行的机器人聊天的结束语句，例如 `Thank you`。

1. 运行 cURL 命令，然后接收包含评分和解答的 JSON 响应。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }

    ```

    由于问题 `Thank you` 与聊天问题完全匹配，因此分数为 100，表明 QnA Maker 完全确信其答案。 QnA Maker 还返回了包含聊天元数据标记信息的所有相关问题和元数据属性。

## <a name="use-curl-to-query-for-the-default-answer"></a>使用 cURL 查询默认解答

QnA Maker 没有把握的问题会收到默认答案。 该答案在 Azure 门户中配置。

1. 在支持 cURL 的终端中，将 `Thank you` 替换为 `x`。

1. 运行 cURL 命令，然后接收包含评分和解答的 JSON 响应。

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```

    QnA Maker 返回的评分为 `0`，表示没有置信度。 它同时返回了默认解答。

## <a name="create-a-knowledge-base-bot"></a>创建知识库机器人

有关详细信息，请参阅[使用此知识库创建聊天机器人](create-qna-bot.md)。

## <a name="clean-up-resources"></a>清理资源

处理完知识库机器人后，请删除资源组 `my-tutorial-rg`，以删除处理机器人过程中创建的所有 Azure 资源。

使用完知识库后，请在 QnA Maker 门户中选择“我的知识库”。  接着选择知识库“我的教程知识库”，然后选择该行最右侧的删除图标。 

## <a name="next-steps"></a>后续步骤

有关支持文件格式的详细信息，请参阅[支持的数据源](../Concepts/content-types.md)。

详细了解聊天[个性化内容](../Concepts/best-practices.md#chit-chat)。

有关默认答案的详细信息，请参阅[找不到匹配项](../Concepts/confidence-score.md#no-match-found)。

> [!div class="nextstepaction"]
> [使用此知识库创建聊天机器人](create-qna-bot.md)