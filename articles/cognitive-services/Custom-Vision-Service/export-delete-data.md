---
title: 导出或删除数据 - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 您可以完全控制数据。 本文介绍如何在自定义影像服务中查看、导出或删除数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718971"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>在自定义视觉中导出或删除用户数据

自定义视觉收集用户数据来操作服务，但客户可以完全控制使用自定义视觉[训练 api](https://go.microsoft.com/fwlink/?linkid=865446)查看、导出和删除其数据。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

若要了解如何在自定义视觉中导出和删除用户数据，请参阅下表。

| 数据 | 导出操作 | 删除操作 |
| ---- | ---------------- | ---------------- |
| 帐户信息（订阅密钥） | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | 使用 Azure 门户（Azure 订阅）进行删除。 或者使用 CustomVision.ai settings 页面（Microsoft 帐户订阅）中的“删除帐户”按钮 | 
| 迭代详细信息 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 迭代性能详细信息 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 迭代列表 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 项目和项目详细信息 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 图像标记 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 映像 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI）和 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI） | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 导出的模型 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 在删除帐户时删除 |
