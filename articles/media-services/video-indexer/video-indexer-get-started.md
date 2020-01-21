---
title: 注册视频索引器并上传第一个视频 - Azure
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器门户注册并上传第一个视频。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: 3a5ddf5bd4614b68e97e7616173a3e0640007530
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941558"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>快速入门：如何注册并上传第一个视频

本入门教程介绍如何登录到视频索引器网站，以及如何上传第一个视频。

创建视频索引器帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频索引器为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以[创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频索引器帐户](connect-to-azure.md)。 你需要为编制索引的分钟数付费，此外还需要支付 Azure 媒体服务帐户相关的费用。 

## <a name="sign-up-for-video-indexer"></a>注册视频索引器

若要开始使用视频索引器进行开发，请浏览到[视频索引器](https://www.videoindexer.com)网站并注册。

## <a name="upload-a-video-using-the-video-indexer-website"></a>使用视频索引器网站上传视频

> [!NOTE]
> 视频的名称长度不得超过 80 个字符。

### <a name="supported-file-formats-for-video-indexer"></a>视频索引器支持的文件格式

有关可用于视频索引器的文件格式列表，请参阅[输入容器/文件格式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)一文。

### <a name="upload-a-video"></a>上传视频

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。
2. 若要上传视频，请按“上传”按钮或链接。 

    ![上载](./media/video-indexer-get-started/video-indexer-upload.png)

    视频上传以后，视频索引器就会开始对视频进行索引编制和分析。

    ![已上传](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    当视频索引器分析完以后，你会获得一个通知，其中包含视频链接以及对视频中找到的内容的简短说明。 例如：人物、主题、OCR。

## <a name="next-steps"></a>后续步骤

有关更多详细信息，请参阅[上传视频并为其编制索引](upload-index-videos.md)。

上传视频并为其编制索引后，可以开始使用[视频索引器](video-indexer-view-edit.md)网站或[视频索引器开发人员门户](video-indexer-use-apis.md)查看视频见解。 

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)

[开始使用 API](video-indexer-use-apis.md)。

