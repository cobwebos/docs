---
title: 在 Azure 内容审查器中审阅文本 | Microsoft Docs
description: 了解如何在内容审查器中审阅文本，以查看分数和检测到的标记。 此类信息可用于确定内容是否合适。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365439"
---
# <a name="review-text"></a>审阅文本

可以在 Azure 内容审查器中通过查看分数和检测到的标记来审阅文本。 此类信息可用于确定内容是否合适。 

## <a name="select-or-enter-the-text-to-review"></a>选择或输入要审阅的文本

在内容审查器中，选择“试用”选项卡。然后，选择“文本”选项，转到文本审查开始屏幕。 输入任何文本，或提交默认示例文本以供自动文本审查。 最多可以输入 1,024 个字符。

## <a name="get-ready-to-review-results"></a>准备查看结果

“审阅”工具先调用文本审查 API， 然后使用检测到的标记生成文本审阅。 “审阅”工具会匹配分数结果，以引起团队的注意。

## <a name="review-text-results"></a>文本审阅结果

详细结果显示在窗口中。 结果包括文本审查 API 返回的检测的标记和术语。 若要切换标记选择状态，请选择标记。 还可以使用可能已创建的任何自定义标记。

![文本审阅结果](images/3-review-text-2.png)
