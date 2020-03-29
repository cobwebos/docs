---
title: 改进知识库 - QnA Maker
description: 通过积极学习提高知识库的质量。 审核、接受或拒绝，添加而不删除或更改现有问题。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071129"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用主动学习改进知识库

[主动学习](../Concepts/active-learning-suggestions.md)通过提出其他问题来提高知识库的质量。 用户提交被考虑在内，并作为建议显示在备用问题列表中。 您可以灵活地将这些建议添加为备选问题或拒绝它们。

知识库不会自动更改。 为了使任何更改生效，您必须接受这些建议。 这些建议会添加问题，但不会更改或删除现有问题。


## <a name="upgrade-runtime-version-to-use-active-learning"></a>升级运行时版本以使用活动学习

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用此功能。

## <a name="turn-on-active-learning-for-alternate-questions"></a>打开活动学习以备候补问题

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 打开活动学习后，您需要将信息从客户端应用发送到 QnA Maker。 有关详细信息，请参阅[使用生成应答的体系结构流，并从自动程序训练 API。](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. 选择 **"发布**"以发布知识库。 仅从生成应答 API 预测终结点收集活动学习查询。 对 QnA Maker 门户中的"测试"窗格的查询不会影响活动学习。

1. 要在 QnA Maker 门户中打开活动学习，请转到右上角，选择 **"您的姓名**"，转到[**"服务设置**](https://www.qnamaker.ai/UserSettings)"。

    ![从"服务设置"页打开活动学习建议的问题备选方案。 在右上角的菜单中选择您的用户名，然后选择"服务设置"。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”****。

    > [!div class="mx-imgBorder"]
    > [![在"服务设置"页上，在"活动学习"功能上切换。如果无法切换该功能，则可能需要升级服务。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上图上的确切版本仅显示为示例。 您的版本可能不同。

    启用 **"主动学习"** 后，知识库会根据用户提交的问题定期提出新问题。 可以通过再次切换设置来禁用“主动学习”****。

## <a name="review-suggested-alternate-questions"></a>查看建议的备用问题

查看每个知识库的 **"编辑"** 页上[的备用建议问题](improve-knowledge-base.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)