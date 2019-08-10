---
title: 通过 "查看" 工具使用内容审阅-内容审查器
titleSuffix: Azure Cognitive Services
description: 了解“审阅”工具如何允许人工审查者审阅 Web 门户中的图像。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: 065d3cd80f93753eb91571d4ada4fe7151258ec0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882659"
---
# <a name="create-human-reviews"></a>创建人审查

在本指南中, 你将了解如何在审阅工具网站上设置[评论](../review-api.md#reviews)。 查看存储并显示要评估的人的内容。 审查人员可以更改应用的标记, 并根据需要应用自己的自定义标记。 当用户完成评审后, 结果将发送到指定的回调终结点, 并从网站中删除内容。

## <a name="prerequisites"></a>先决条件

- 登录或创建内容审查器[审核工具](https://contentmoderator.cognitive.microsoft.com/)站点上的帐户。

## <a name="image-reviews"></a>图像评审

1. 请参阅 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com/), 选择 "**试用**" 选项卡, 并上传要查看的一些图像。
1. 上传的图像处理完毕后, 请进入 "**查看**" 选项卡, 然后选择 "**图像**"。

    ![显示审阅工具的 Chrome 浏览器，其中突出显示了“审阅图像”选项](images/review-images-1.png)

    图像显示自动审核过程所分配的任何标签。 通过查看工具提交的图像对其他审阅者不可见。

1. 或者, 将 "**评论**" 移到 "显示滑块" (1) 以调整屏幕上显示的图像的数量。 单击标记或未**标记**的按钮 (2) 以相应地对图像进行排序。 单击标签面板 (3) 以打开或关闭它。

    ![Chrome 浏览器显示带有标记图片的审阅工具以供审阅](images/review-images-2.png)

1. 若要查看有关图像的详细信息, 请单击缩略图中的省略号, 然后选择 "**查看详细**信息"。 可以使用 "**移动到**" 选项将图像分配给具有 (请参阅 "[团队](./configure.md#manage-team-and-subteams)" 部分, 了解有关子组的详细信息)。

    ![突出显示“查看详细信息”选项的图像](images/review-images-3.png)

1. 浏览详细信息页面上的图像审阅信息。

    ![在单独窗格中列出审阅详细信息的图像](images/review-images-4.png)

1. 根据需要审核并更新标记分配后，单击“下一步”即可提交评论。 提交后，你有大约五秒的时间来单击“上一步”按钮返回上一屏幕并再次查看图像。 之后，图像不再位于“提交”队列中，并且“上一步”按钮不再可用。

## <a name="text-reviews"></a>文本评审

文本审阅功能类似于图像审阅。 只需编写或粘贴文本 (最多1024个字符), 而不是上载内容。 然后, 内容审查器会分析文本, 并应用标记 (除了猥亵和个人数据等其他审核信息)。 在文本评审中, 你可以在提交评审之前切换应用的标记和/或应用自定义标记。

![评审工具的屏幕截图，显示 Chrome 浏览器窗口中的已标记文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>后续步骤

在本指南中, 您学习了如何设置和使用内容审查器[审阅工具](https://contentmoderator.cognitive.microsoft.com)中的评论。 接下来, 请参阅[REST API 指南](../try-review-api-review.md)或[.net SDK 指南](../moderation-reviews-quickstart-dotnet.md)以了解如何以编程方式创建评论。