---
title: 资产
titleSuffix: Azure Media Services
description: 了解 Azure 媒体服务是什么资产以及如何使用它们。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68bb1fea88ab7ba30e0ba07839f2d962840b7818
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921175"
---
# <a name="assets-in-azure-media-services"></a>Azure 媒体服务中的资产

在 Azure 媒体服务中，[资产](https://docs.microsoft.com/rest/api/media/assets)是核心概念。 您可以在其中输入媒体（例如，通过上传或实时摄取）、输出媒体（从作业输出）以及从中发布媒体（用于流式处理）。 

资产将映射到 [Azure 存储帐户](storage-account-concept.md)中的 Blob 容器，资产中的文件作为块 Blob 存储在该容器中。 资产包含有关存储在 Azure 存储空间中的数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）的信息。

当帐户使用常规用途 v2 (GPv2) 存储时，媒体服务支持 Blob 层。 使用 GPv2 可将文件移到[冷存储或存档存储](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 **存档**存储适用于在不再需要时存档源文件（例如，在编码后）。

建议仅将**存档**存储用于已编码的，并且其编码作业输出已放入输出 Blob 容器中的极大型源文件。 要与资产关联并使用流式处理或分析内容的输出容器中的 blob 必须存在于**热**或**冷**存储层中。

## <a name="naming"></a>命名 

### <a name="assets"></a>资产

资产的名称必须是唯一的。 媒体服务 v3 资源名称（例如，资产、作业、转换）受 Azure 资源管理器命名约束的约束。 有关详细信息，请参阅[命名约定](media-services-apis-overview.md#naming-conventions)。

### <a name="blobs"></a>Blob

资产内文件/blob 的名称必须遵循[blob 名称要求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和[NTFS 名称要求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 这些要求的原因是，可将文件从 blob 存储复制到本地 NTFS 磁盘进行处理。

## <a name="map-v3-asset-properties-to-v2"></a>将 v3 资产属性映射到 v2

下表显示了 v3 中[资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)的属性映射到 V2 中资产的属性的方式。

|v3 属性|v2 属性|
|---|---|
|`id`-（唯一）完整的 Azure 资源管理器路径，请参阅[资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate)中的示例||
|`name`-（唯一）请参阅[命名约定](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-（唯一）值以 `nb:cid:UUID:` 前缀开头。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` （创建选项）|
|`type`||

## <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务中的工作方式：

|加密选项|说明|媒体服务 v2|媒体服务 v3|
|---|---|---|---|
|媒体服务存储加密|AES-256 加密，密钥由媒体服务管理。|支持<sup>(1)</sup>|不支持<sup>(2)</sup>|
|[静态数据的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 存储提供的服务器端加密、由 Azure 或客户管理的密钥。|支持|支持|
|[存储客户端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 存储提供的客户端加密，Key Vault 中的客户管理的密钥。|不支持|不支持|

<sup>1</sup>虽然媒体服务支持以明文/不加密形式处理内容，但不建议这样做。

<sup>2</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 意思是，v3 适用于现有的存储加密资产，但不允许创建新的资产。

## <a name="next-steps"></a>后续步骤

[管理媒体服务中的资产](manage-asset-concept.md)

## <a name="also-see"></a>另请参阅

[媒体服务 v2 与 v3 之间的差别](migrate-from-v2-to-v3.md)
