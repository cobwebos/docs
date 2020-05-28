---
title: 向 QnA Maker 知识库添加聊天内容
titleSuffix: Azure Cognitive Services
description: 在创建 KB 时向机器人中添加个性化聊天内容可使其更健谈而有趣。 使用 QnA Maker，可以轻松将预填充的一组最常用的聊天内容添加到知识库中。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e06cad8e8ca62330b09e173cf6058905b49f19e4
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992899"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>向知识库添加聊天内容

向机器人中添加聊天内容可使其更健谈而有趣。 使用 QnA Maker 中的聊天功能，可以轻松地将预填充的一组最常用的聊天内容添加到知识库 (KB) 中。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。

此数据集具有大约100的 chit 方案-在多个角色（如专业、友好和 Witty）的语音中聊天。 选择与机器人的语音最接近的角色。 对于给定的用户查询，QnA Maker 会尝试将其与最接近的已知聊天内容 QnA 匹配。

下面是一些不同个性的示例。 你可以看到所有的个性[数据集](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)以及个性的详细信息。

对于的用户查询 `When is your birthday?` ，每个用户都有一个有样式的响应：

<!-- added quotes so acrolinx doesn't score these sentences -->
|个性化|示例|
|--|--|
|Professional|年龄并不真正适用于我。|
|友好|我真的没有时间了。|
|Witty|我是免费的。|
|关心|我没有年龄。|
|热情|我是 bot，所以没有时间。|
||


## <a name="language-support"></a>语言支持

Chit-支持以下语言的聊天数据集：

|语言|
|--|
|中文|
|英语|
|法语|
|德国|
|意大利语|
|日语|
|韩语|
|葡萄牙语|
|西班牙语|


## <a name="add-chit-chat-during-kb-creation"></a>在 KB 创建过程中添加聊天内容
在知识库创建过程中，在添加源 URL 和文件以后，就会有一个添加聊天内容的选项。 选择需要用作聊天内容库的个性。 如果不希望添加聊天内容，或者数据源中已经有聊天内容支持，请选择“无”。****

## <a name="add-chit-chat-to-an-existing-kb"></a>向现有 KB 添加聊天内容
选择 KB，导航到“设置”页。**** 有一个链接，指向所有采用相应 **.tsv** 格式的聊天内容数据集。 下载所需个性，然后将其作为文件源上传。 请确保在下载和上传文件时不编辑格式或元数据。

![向现有 KB 添加聊天内容](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>编辑聊天内容的问题和解答
编辑 KB 时，会看到一个适用于聊天内容的新源，具体取决于所选个性。 现在可以添加更改的问题或编辑响应，就像使用任何其他的源一样。

![编辑聊天内容 QnA](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

若要查看元数据，请在工具栏中选择 "**查看选项**"，然后选择 "**显示元数据**"。

## <a name="add-additional-chit-chat-questions-and-answers"></a>添加其他的聊天内容问题和解答
可以添加不在预定义数据集中的新 chit/聊天 QnA 对。 确保不复制聊天内容集中已涵盖的 QnA 对。 添加任何新的聊天内容 QnA 时，它会添加到“编辑”源。**** 若要确保 ranker 了解这是 chit 聊天，请添加元数据键/值对 "社论： chitchat"，如下图所示：

![![Add chit-chat Qna](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>从现有 KB 中删除聊天内容
选择 KB，导航到“设置”页。**** 特定的聊天内容源作为文件列出，使用所选的个性名称。 可以将其作为源文件删除。

![从 KB 中删除聊天内容](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入知识库](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>另请参阅

[QnA Maker 概述](../Overview/overview.md)
