---
title: 将活动学习与知识库结合使用-QnA Maker
description: 通过主动学习提高知识库的质量。 查看、接受或拒绝，添加时不删除或更改现有问题。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 004ee9d4cced0116831718b1601d5d510dea5a4d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090920"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用主动学习改进知识库

利用[活动学习](../Concepts/active-learning-suggestions.md)，可以通过建议替代问题来提高知识库的质量。 需要考虑用户提交，并在替代问题列表中显示为建议。 您可以灵活地将这些建议添加为替代问题或拒绝它们。

知识库不会自动更改。 要使更改生效，必须接受建议。 这些建议会添加问题，但不会更改或删除现有问题。


## <a name="upgrade-runtime-version-to-use-active-learning"></a>升级运行时版本以使用活动学习

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用此功能。

## <a name="turn-on-active-learning-for-alternate-questions"></a>启用其他问题的活动学习

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 开启活动学习后，需要将客户端应用程序中的信息发送到 QnA Maker。 有关详细信息，请参阅[使用 GenerateAnswer 的体系结构流和从机器人训练 api](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 选择 "**发布**" 以发布知识库。 仅从 GenerateAnswer API 预测终结点收集活动学习查询。 QnA Maker 门户中的 "测试" 窗格查询不会影响主动学习。

1. 若要在 QnA Maker 门户中打开活动学习，请转到右上角，选择你的**名称**，转到 "[**服务设置**](https://www.qnamaker.ai/UserSettings)"。

    ![从 "服务设置" 页中打开活动学习的建议问题替代方法。 在右上方菜单中选择您的用户名，然后选择 "服务设置"。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”****。

    > [!div class="mx-imgBorder"]
    > [![在 "服务设置" 页上，切换活动学习功能。如果无法切换该功能，则可能需要升级您的服务。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上图中的确切版本仅显示为示例。 你的版本可能不同。

    启用**活动学习**后，该知识库将基于用户提交的问题，定期提出新问题。 可以通过再次切换设置来禁用“主动学习”****。

## <a name="review-suggested-alternate-questions"></a>查看建议的替代问题

查看每个知识库的 "**编辑**" 页上的[替代建议问题](improve-knowledge-base.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)