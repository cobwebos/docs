---
title: 在视频索引器中自定义人员模型 - Azure
titlesuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的人员模型，以及如何自定义它。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c74b913fc3ac35039d914fc97c9c438d2e4a3069
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799429"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在视频索引器中自定义人员模型

视频索引器支持在视频中的名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 视频索引器不能识别的人脸仍检测到，但会保留未命名。 客户可以构建自定义的人员模型，使视频索引器能够识别默认情况下无法识别的人脸。 客户可以生成这些人员模型与图像文件的人员的人脸配对某个人的姓名。  

如果你的帐户适用于不同的用例，您可以受益于能够创建每个帐户的多个用户模型。 例如，如果你的帐户中的内容要将其分类到不同的通道，您可能想要创建单独的人员模型为每个通道。 

> [!NOTE]
> 每个用户模型支持多达 1 百万人，每个帐户的限制为 50 人模型。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 培训新的视频人脸，更新特定视频与关联的自定义模型。 

如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频索引器将在你的帐户使用默认个人模型。 

您可以使用视频索引器网站编辑视频中检测到的人脸并管理你的帐户，在多个自定义的人员模型中所述[自定义使用网站的人员模型](customize-person-model-with-website.md)主题。 也可以使用 API，如中所述 [自定义使用 Api 的人模型](customize-person-model-with-api.md)。