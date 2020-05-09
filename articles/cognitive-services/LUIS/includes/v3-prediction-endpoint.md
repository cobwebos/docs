---
title: 如何获取 V3 预测终结点
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287741"
---
1. 在 LUIS 门户中的“Azure 资源”页面（左菜单）上的“预测资源”选项卡的“管理”部分（右上菜单），复制页面底部的“示例查询”     。

    将 URL 粘贴到新的浏览器选项卡中。

    该 URL 具有应用 ID、密钥和槽名称。 V3 预测终结点 URL 如下所示：

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

