---
title: 审阅带标记的图像 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 了解“审阅”工具如何允许人工审查者审阅 Web 门户中的图像。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e096e65e3016f33361f772a75ab8f71603970a5f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096599"
---
# <a name="let-human-reviewers-review-images"></a>允许人工审阅者审阅图像

注册内容审核并获得订阅密钥后，你可以尝试使用图像审阅功能。

1. 打开[人工审阅工具](https://contentmoderator.cognitive.microsoft.com/)并登录。 
2. 单击“试用”选项卡，然后上传一些图像以进行审阅。
3. 单击“审阅”选项卡，然后选择“图像”。

   ![显示审阅工具的 Chrome 浏览器，其中突出显示了“审阅图像”选项](images/review-images-1.png)

   图像通过由审阅工具分配的任何标签显示。 查看这些图片时，团队中的其他审阅者无法使用这些图像。

4. 移动“要显示的审阅图像”滑块 (1) 以调整屏幕上显示的图像数量。 单击已标记或未标记的按钮 (2) 以相应地对图像进行排序。 单击标记 (3) 以打开或关闭它。

   ![Chrome 浏览器显示带有标记图片的审阅工具以供审阅](images/review-images-2.png)
 
5. 要查看有关图像的详细信息，请单击缩略图上的省略号，然后单击“查看详细信息”选项。 要将图像分配给子团队，请选择“移至”选项。
 
   ![突出显示“查看详细信息”选项的图像](images/review-images-3.png)

6. 浏览详细信息页面上的图像审阅信息。

   ![在单独窗格中列出审阅详细信息的图像](images/review-images-4.png)
 
7. 根据需要审核并更新标记分配后，单击“下一步”即可提交评论。

提交后，你有大约五秒的时间来单击“上一步”按钮返回上一屏幕并再次查看图像。 之后，图像不再位于“提交”队列中，并且“上一步”按钮不再可用。
