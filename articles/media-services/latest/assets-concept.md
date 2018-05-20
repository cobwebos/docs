---
title: Azure 媒体服务中的资产 | Microsoft Docs
description: 本文介绍何为资产以及 Azure 媒体服务如何使用这些资产。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>资产

**资产**包含数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）以及这些文件的相关元数据。 数字文件在上传到资产中后，即可用于媒体服务编码和流式处理工作流。

资产会被映射到 [Azure 存储帐户](storage-account-concept.md)中的 blob 容器，而资产中的文件会作为块 blob 存储在该容器中。 可以使用存储 SDK 客户端与容器中的资产文件进行交互。

当帐户使用常规用途 v2 (GPv2) 存储时，Azure 媒体服务支持 Blob 层。 借助 GPv2，可以将文件移动到冷存储。 冷存储适合存储不再需要的夹层文件（例如，编码后的夹层文件）。

在媒体服务 v3 中，可以从资产或 HTTP URL 创建作业输入。 若要创建可用作作业输入的资产，请参阅[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。

此外，请参阅[媒体服务中的存储帐户](storage-account-concept.md)和[转换和作业](transform-concept.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [流式传输文件](stream-files-dotnet-quickstart.md)
