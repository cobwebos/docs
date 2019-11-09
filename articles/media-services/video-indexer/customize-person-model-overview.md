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
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838294"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在视频索引器中自定义人员模型

视频索引器支持视频中的名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 仍检测到视频索引器无法识别的面部面，但仍未命名。 客户可以生成自定义人员模型，并启用视频索引器来识别默认情况下无法识别的人脸。 客户可以通过将人员的姓名与人脸的图像文件配对来构建这些人员的模型。  

如果你的帐户适用于到不同用例，则可以从为每个帐户创建多个人员模型中获益。 例如，如果你的帐户中的内容将被分类到不同的频道，你可能希望为每个频道创建一个单独的人员模型。 

> [!NOTE]
> 每个人员模型最多支持1000000人，并且每个帐户都有50个人员模型。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频定型全新面，更新与视频关联的特定自定义模型。 

如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频索引器将使用帐户中的默认人员模型。 

您可以使用视频索引器网站来编辑在视频中检测到的人脸，并在您的帐户中管理多个自定义人员模型，如[使用网站自定义人员模型](customize-person-model-with-website.md)主题中所述。 你还可以使用 API，如 [使用 Api 自定义人员模型](customize-person-model-with-api.md)中所述。
