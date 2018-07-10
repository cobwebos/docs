---
title: 在内容审查器中导出或删除数据 - Azure 认知服务 | Microsoft Docs
description: 了解如何在内容审查器中导出或删除数据。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366725"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>在内容审查器中导出或删除用数据

内容审查器收集用户数据来使服务运转，但客户拥有完全控制权限使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/) 和 [API](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference) 来查看、导出和删除其数据。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

有关如何在内容审查器中导出和删除用户数据的详细信息，请参阅下表。

| 数据 | 导出操作 | 删除操作 |
| ---- | ---------------- | ---------------- |
| 帐户信息（订阅密钥） | 不适用 | 使用 Azure 门户（Azure 订阅）进行删除。 或者使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/)“团队设置”页面中的“删除团队”按钮。 |
| 用于自定义匹配的图像 | [获取图像 ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)。 映像以单向专有哈希格式存储，并且无法提取实际映像。 | [删除所有图像](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)。 或者使用 Azure 门户删除内容审查器资源。 |
| 用于自定义匹配的术语 | [获取所有术语](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [删除所有术语](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d) 或者使用 Azure 门户删除内容审查器资源。 |
| 标记 | 不适用 | 使用评审 UI 中“标记设置”页面上可用于每个标记的“删除”图标 或者使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/)“团队设置”页面中的“删除团队”按钮。 |
| 评审 | [获取评审](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | 使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/)“团队设置”页面中的“删除团队”按钮。
| 用户 | 不适用 | 使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/)“团队设置”页面上可用于每个用户的“删除”图标。 或者使用[评审 UI](http://contentmoderator.cognitive.microsoft.com/)“团队设置”页面中的“删除团队”按钮。 |

