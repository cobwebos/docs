---
title: 资产
titleSuffix: Azure Media Services
description: 介绍何为资产以及 Azure 媒体服务如何使用这些资产。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 99101f31ec955c822c784180dd8b04bfb8e5f73c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291596"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure 媒体服务 v3 中的资产

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒体服务中，[资产](/rest/api/media/assets)是一个核心概念。 你可以在其中输入媒体（例如，通过上传或实时引入）、输出媒体（从作业输出）以及从中发布媒体（用于流式处理）。 

资产将映射到 [Azure 存储帐户](storage-account-concept.md)中的 Blob 容器，资产中的文件作为块 Blob 存储在该容器中。 资产包含有关 Azure 存储中存储的数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）的信息。

当帐户使用常规用途 v2 (GPv2) 存储时，媒体服务支持 Blob 层。 使用 GPv2 可将文件移到[冷存储或存档存储](../../storage/blobs/storage-blob-storage-tiers.md)。 **存档**存储适合存档不再需要的源文件（例如，编码后的源文件）。

建议仅将**存档**存储用于已编码的，并且其编码作业输出已放入输出 Blob 容器中的极大型源文件。 要与资产关联并使用流式处理或分析内容的输出容器中的 blob 必须存在于 **热** 或 **冷** 存储层中。

## <a name="naming"></a>命名 

### <a name="assets"></a>资产

资产的名称必须唯一。 媒体服务 v3 资源名称（例如，资产、作业、转换）受 Azure 资源管理器命名约束的制约。 有关详细信息，请参阅[命名约定](media-services-apis-overview.md#naming-conventions)。

### <a name="blobs"></a>Blob

资产中的文件/blob 名称必须符合 [Blob 名称要求](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)和 [NTFS 名称要求](/windows/win32/fileio/naming-a-file)。 符合这些要求的原因是可以将文件从 Blob 存储复制到本地 NTFS 磁盘进行处理。

## <a name="next-steps"></a>后续步骤

[媒体服务概述](media-services-overview.md)

## <a name="see-also"></a>另请参阅

[媒体服务 v2 与 v3 之间的差别](migrate-from-v2-to-v3.md)
