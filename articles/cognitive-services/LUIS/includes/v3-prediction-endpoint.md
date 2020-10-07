---
title: 如何获取 V3 预测终结点
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545453"
---
1. 在 LUIS 门户中的“Azure 资源”页面（左菜单）上的“预测资源”选项卡的“管理”部分（右上菜单）中，复制页面底部的“示例查询”   。

    将 URL 粘贴到新的浏览器选项卡中。

    该 URL 具有应用 ID、密钥和槽名称。 V3 预测终结点 URL 如下所示：

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

