---
title: 获取订阅密钥 - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 了解如何在 Azure 认知服务中获取计算机视觉 API 所需的订阅密钥。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 08838ce0af16cc4ae768bd5d2ecf72c57f8fae97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858070"
---
# <a name="how-to-obtain-subscription-keys"></a>如何获取订阅密钥

计算机视觉服务需要特殊的订阅密钥。 每次调用计算机视觉 API 都需要订阅密钥。 此密钥需要通过查询字符串参数传递，或者在请求头中指定。

若要注册订阅密钥，请参阅[订阅](https://azure.microsoft.com/try/cognitive-services/)。 注册免费。 这些服务的定价随时可能更改。

>[!NOTE]
订阅密钥仅对 [Microsoft Azure 区域](https://azure.microsoft.com/regions/)之一有效。 

| 区域 | 地址 |
|---|---|
| 美国西部 | westus.api.cognitive.microsoft.com |
| 美国东部 2 | eastus2.api.cognitive.microsoft.com |
| 美国中西部 | westcentralus.api.cognitive.microsoft.com |
| 西欧 | westeurope.api.cognitive.microsoft.com |
| 东南亚 | southeastasia.api.cognitive.microsoft.com |

如果使用计算机视觉免费试用版注册，订阅密钥将对中西部区域 (`https://westcentralus.api.cognitive.microsoft.com/`) 有效。 这是最常见的情况。 但是，如果通过 [https://azure.microsoft.com/](https://azure.microsoft.com/) 网站使用 Microsoft Azure 帐户注册计算机视觉，将从前面的区域列表中指定免费版的区域。

例如，如果使用 Microsoft Azure 帐户注册计算机视觉，并且指定 `westus` 区域，则必须使用 `westus` 区域，用于 REST API 调用 (`https://westus.api.cognitive.microsoft.com/`)。

如果在获取试用版密钥后忘记订阅密钥的区域，可在 [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/) 查找区域。

### <a name="related-links"></a>相关链接：

* [Azure 认知服务 API 的定价选项](https://azure.microsoft.com/pricing/details/cognitive-services/)
