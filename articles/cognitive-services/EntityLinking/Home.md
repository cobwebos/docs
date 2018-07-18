---
title: 实体链接智能服务 API | Microsoft Docs
description: 了解如何使用认知服务中的实体链接智能服务 API 执行实体链接任务。
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: 662295c42a421fe76892e6e5aac7b7e7178d6155
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365461"
---
# <a name="entity-linking-intelligence-service-api"></a>实体链接智能服务 API

欢迎使用 Microsoft 实体链接智能服务，这是一项有助于开发人员完成实体链接相关任务的 Web 服务。

## <a name="entity-linking"></a>实体链接

有时，给定句子中的一个字词可能会在不同上下文中用作命名实体、谓词或其他词形。 例如，假设“times”（时代）是命名实体，它仍可能指代两个可区分的单独实体，如“The New York Times”（《纽约时报》）或“Times Square”（时代广场）。 对于文档中的特定段落，实体链接智能服务会根据上下文识别和标识各个实体。  

下图展示了实体链接示例。 具体来说，使用维基百科时，实体链接智能服务会检测输入文本中提到的所有实体，并根据页面 ID 将它们链接到相关参考点。

 ![Mars 实体链接示例](./Images/EntityLinkingSample1.png)
 
## <a name="get-started"></a>入门
 
若要快速浏览所有实体链接基本功能和订阅过程，请参阅入门教程。
[C# 实体链接 API 入门](GettingStarted.md)


