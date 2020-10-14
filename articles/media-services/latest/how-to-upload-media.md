---
title: 上传媒体
titleSuffix: Azure Media Services
description: 了解如何上传媒体以进行流式处理或编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3040369e655ab91f56f271313dc4d2613f02be06
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015835"
---
# <a name="upload-media-for-streaming-or-encoding"></a>上传媒体以进行流式处理或编码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在媒体服务中，可以将数字文件（媒体）上传到与资产关联的 blob 容器中。 [资产](/rest/api/media/operations/asset)实体可以包含视频、音频、图片、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据）。 将文件上传到资产的容器中后，相关内容即安全地存储在云中供后续处理和流式传输。

不过，在开始之前，你需要收集或考虑一些值。

1. 想要上传的文件的本地文件路径
1. 资产（容器）的资产 ID
1. 想要用于已上传文件（包括扩展）的名称
1. 要使用的存储帐户的名称
1. 存储帐户的访问密钥

## <a name="portal"></a>[门户](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

[使用 Postman 或其他 REST 方法创建资产并且获取该资产的 SAS URL](how-to-create-asset.md?tabs=rest) 之后，使用 Azure 存储 API 或 SDK（例如[存储 REST API](../../storage/common/storage-rest-api-auth.md) 或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）。

---
<!-- add these to the tabs when available -->
有关其他方法，请参阅 [Azure 存储文档](../../storage/blobs/index.yml)以了解如何在 [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[Java](../../storage/blobs/storage-quickstart-blobs-java.md)、[Python](../../storage/blobs/storage-quickstart-blobs-python.md) 和 [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md) 中使用 blob。

## <a name="next-steps"></a>后续步骤

> [媒体服务概述](media-services-overview.md)