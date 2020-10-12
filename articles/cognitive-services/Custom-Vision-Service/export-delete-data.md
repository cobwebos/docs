---
title: 查看或删除数据-自定义影像服务
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
ms.openlocfilehash: fe17fa4349085be47201974b418493fe8d912ece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527388"
---
# <a name="view-or-delete-user-data-in-custom-vision"></a>查看或删除自定义视觉中的用户数据

自定义视觉收集用户数据来操作服务，但客户可以完全控制使用自定义视觉 [训练 api](https://go.microsoft.com/fwlink/?linkid=865446)查看和删除其数据。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

若要了解如何查看和删除自定义视觉中的用户数据，请参阅下表。

| 数据 | 查看操作 | 删除操作 |
| ---- | ---------------- | ---------------- |
|  (订阅密钥) 的帐户信息 | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | 使用 Azure 门户（Azure 订阅）进行删除。 或使用 "CustomVision.ai 设置" 页中的 "删除帐户" 按钮 (Microsoft 帐户订阅)  | 
| 迭代详细信息 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 迭代性能详细信息 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 迭代列表 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| 项目和项目详细信息 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 映像标记 | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) 和 [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 映像 | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI）和 [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446)（提供用于图像下载的 URI） | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 导出的迭代 | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | 在删除帐户时删除 |
