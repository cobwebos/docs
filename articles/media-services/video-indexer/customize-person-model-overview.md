---
title: 在视频索引器中自定义人员模型 - Azure
titleSuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的人员模型，以及如何自定义它。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838294"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在视频索引器中自定义人员模型

视频索引器支持视频中的名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 视频索引器无法识别的面仍被检测到，但未命名。 客户可以构建自定义人员模型，并使视频索引器能够识别默认情况下无法识别的面。 客户可以通过将人员姓名与人脸的图像文件配对来构建这些人员模型。  

如果您的帐户适合不同的用例，则可以从能够创建每个帐户的多个人员模型中获益。 例如，如果帐户中的内容要分到不同的通道中，则可能需要为每个通道创建单独的 Person 模型。 

> [!NOTE]
> 每个人员模型最多支持 100 万人，每个帐户有 50 人模型的限制。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新面孔，更新视频关联的特定自定义模型。 

如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频索引器将在您的帐户中使用默认的"人"模型。 

您可以使用视频索引器网站编辑视频中检测到的人脸，并管理帐户中的多个自定义人员模型，如[使用网站主题自定义人员模型](customize-person-model-with-website.md)中所述。 您还可以使用 API，如 [使用 API 自定义人员模型](customize-person-model-with-api.md)中所述。
