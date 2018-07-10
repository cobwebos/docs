---
title: 在自定义视觉（Azure 认知服务）中导出或删除数据 | Microsoft Docs
description: 了解如何在自定义视觉中导出或删除数据。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366727"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>在自定义视觉中导出或删除用户数据

内容审查器收集用户数据来使服务运转，但客户拥有完全控制权限使用自定义影像服务[训练 API](https://go.microsoft.com/fwlink/?linkid=865446) 来查看、导出和删除其数据。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

有关如何在自定义视觉中导出和删除用户数据的详细信息，请参阅下表。

| 数据 | 导出操作 | 删除操作 |
| ---- | ---------------- | ---------------- |
| 帐户信息（订阅密钥） | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | 使用 Azure 门户（Azure 订阅）进行删除。 或者使用 CustomVision.ai settings 页面（Microsoft 帐户订阅）中的“删除帐户”按钮 |
| 迭代详细信息 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 迭代性能详细信息 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 迭代列表 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 项目和项目详细信息 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| 图像标记 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| 图像 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI）和 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI） | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| 导出的模型 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 在删除帐户时删除 |
