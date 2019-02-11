---
title: 在视频索引器中自定义人员模型 - Azure
titlesuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的人员模型，以及如何自定义它。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283233"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在视频索引器中自定义人员模型


视频索引器支持视频内容的人脸检测和名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约 1,000,000 张人脸。 会检测到名人识别功能未识别的人脸；但是，这些人脸会保留未命名状态。 将视频上传到视频索引器并获得结果后，你可以返回并命名未识别的人脸。 使用名称标记人脸后，人脸和名称将添加到你的帐户的人员模型中。 然后，视频索引器会在将来的视频和过去的视频中识别出这此人脸。

可以使用“视频索引器”网站或 API 在帐户中编辑视频中检测到的人脸，如以下主题中所述：

- [使用 API 自定义人员模型](customize-person-model-with-api.md)
- [使用网站自定义人员模型](customize-person-model-with-website.md)
