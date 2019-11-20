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
ms.openlocfilehash: ab4eebf56abd2d328ccf86929a043d4354ca157c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186317"
---
# <a name="assets-in-azure-media-services"></a>Azure 媒体服务中的资产

在 Azure 媒体服务中，[资产](https://docs.microsoft.com/rest/api/media/assets)包含有关 Azure 存储中存储的数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）的信息。

资产将映射到 [Azure 存储帐户](storage-account-concept.md)中的 Blob 容器，资产中的文件作为块 Blob 存储在该容器中。 当帐户使用常规用途 v2 (GPv2) 存储时，媒体服务支持 Blob 层。 使用 GPv2 可将文件移到[冷存储或存档存储](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。 **存档**存储适用于在不再需要时存档源文件（例如，在编码后）。

建议仅将**存档**存储用于已编码的，并且其编码作业输出已放入输出 Blob 容器中的极大型源文件。 要与资产关联并使用流式处理或分析内容的输出容器中的 blob 必须存在于**热**或**冷**存储层中。

### <a name="naming-blobs"></a>命名 blob

资产中的文件/blob 名称必须符合 [Blob 名称要求](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)和 [NTFS 名称要求](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)。 符合这些要求的原因是可以将文件从 Blob 存储复制到本地 NTFS 磁盘进行处理。

## <a name="upload-digital-files-into-assets"></a>将数字文件上传到资产

将数字文件上传到存储区并将其与资产相关联后，可在媒体服务编码、流式传输和分析内容工作流中使用它们。 一个常见的媒体服务工作流是上传、编码和流式传输文件。 本部分概述常规步骤。

> [!TIP]
> 开始开发之前，请查看[通过媒体服务 V3 api 进行开发](media-services-apis-overview.md)（包括有关访问 api、命名约定等的信息）。

1. 使用媒体服务 v3 API 创建新的“输入”资产。 此操作在与媒体服务帐户关联的存储帐户中创建一个容器。 API 返回容器名称（例如 `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`）。

    如果已有一个要与资产关联的 blob 容器，则可以在创建资产时指定容器名称。 媒体服务目前仅支持容器根中的 Blob，而不支持在文件名中输入路径。 因此，使用文件名“input.mp4”的容器是有效的。 但是，带有 "视频/输入/输入.." 的容器不起作用。

    可以使用 Azure CLI 直接上传到订阅中你有权访问的任何存储帐户和容器。

    容器名称必须唯一，并遵循存储命名准则。 该名称不一定要遵循媒体服务资产容器名称（资产 GUID）的格式。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 获取具有读写权限的 SAS URL，用于将数字文件上传到资产容器。 可以使用媒体服务 API [列出资产容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。
3. 使用 Azure 存储 API 或 SDK（例如，[存储 REST API](../../storage/common/storage-rest-api-auth.md) 或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）将文件上传到资产容器。
4. 使用媒体服务 v3 API 创建用于处理“输入”资产的转换和作业。 有关详细信息，请参阅[转换和作业](transform-concept.md)。
5. 从“输出”资产流式传输内容。

有关演示如何创建资产、在存储中获取资产容器的可写 SAS URL 并将该文件上传到使用 SAS URL 的存储中的容器的完整 .NET 示例，请参阅[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。

### <a name="create-a-new-asset"></a>创建新资产

> [!NOTE]
> Datetime 类型的资产属性始终采用 UTC 格式。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

有关 REST 示例，请参阅[使用 REST 创建资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)示例。

该示例演示如何创建**请求正文**，你可以在其中指定说明、容器名称、存储帐户和其他有用的信息。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

有关完整示例，请参阅[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。 在媒体服务 v3 中，还可以从 HTTPS URL 创建作业的输入（请参阅[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)）。

## <a name="map-v3-asset-properties-to-v2"></a>将 v3 资产属性映射到 v2

下表显示了 v3 中[资产](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)的属性如何映射到 v2 中资产的属性。

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

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [流式传输文件](stream-files-dotnet-quickstart.md)
* [使用云 DVR](live-event-cloud-dvr.md)
* [媒体服务 v2 与 v3 之间的差别](migrate-from-v2-to-v3.md)
