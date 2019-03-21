---
title: 创建、训练、发布知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 此示例中的 QnA Maker KB 根据一个简单的常见问题解答网页创建，该网页解答有关 BitLocker 密钥恢复的问题。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 3f7b24af3d48075150ae7c192ccf421b928a7aa3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113693"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>创建、训练和发布 QnA Maker 知识库

可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 此示例中的 QnA Maker KB 根据一个简单的常见问题解答网页创建，该网页解答有关 BitLocker 密钥恢复的问题。

## <a name="prerequisite"></a>先决条件

> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-qna-maker-knowledge-base"></a>创建 QnA Maker 知识库

1. 使用 Azure 凭据登录到 QnAMaker.ai。

2. 在 QnA Maker 网站上，选择“创建知识库”。

   ![新建 KB](../media/qna-maker-create-kb.png)

3. 在步骤 1 的“创建”页上，选择“创建 QnA 服务”。 此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 如果 Azure 门户超时，请在站点上选择“重试”。 连接后，Azure 仪表板会显示。

4. 在 Azure 中成功创建新的 QnA Maker 服务以后，返回到 qnamaker.ai/create。 在步骤 2 中，从下拉列表选择 QnA 服务。 如果已创建新的 QnA 服务，请确保刷新页面。

   ![选择 QnA 服务 KB](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. 在步骤 3 中，将 KB 命名为“我的示例 QnA KB”。

6. 若要向 KB 添加内容，请选择三种类型的数据源。 在步骤 4 中，在“填充 KB”下的“URL”框中添加 [BitLocker 恢复常见问题解答](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL。

   ![选择 QnA 服务 KB](../media/qnamaker-quickstart-kb/add-datasources.png)

7. 在步骤 5 中，选择“创建 KB”。

8. 在创建 KB 时，会显示一个弹出窗口。 提取过程需要数分钟来读取 HTML 页面并确定问题和解答。

9. 成功创建 KB 以后，“知识库”页面会打开。 可以在此页面上编辑 KB 的内容。

10. 在右上角选择“添加 QnA 对”，以便在 KB 的“编辑”部分添加新的行。 在“问题”下输入“嗨”。 在“解答”下输入“你好，请向我提问 Bitlocker 问题。”

    ![添加 QnA 对](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. 在右上角选择“保存并训练”，以便保存所做的编辑并训练 QnA Maker 模型。 如果不保存，编辑的内容不会保留。

12. 在右上角选择“测试”，以便测试所做的更改是否已生效。 在框中输入“`hi there`”，然后选择 Enter。 此时会看到已创建的作为响应的解答。

13. 选择“检查”，更详细地检查响应。 测试窗口用于测试对 KB 所做的更改，然后会发布这些更改。

    ![测试面板](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. 再次选择“测试”，关闭“测试”弹出窗口。

15. 在“编辑”旁边的菜单中，选择“发布”。 然后，若要进行确认，请在页面上选择“发布”。

16. QnA Maker 服务现在已成功发布。 可以在应用程序或机器人代码中使用此终结点。

    ![发布](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](../How-To/create-knowledge-base.md)
