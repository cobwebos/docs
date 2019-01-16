---
title: 如何将 Blob 添加到 Azure 数字孪生中的对象 | Microsoft Docs
description: 了解如何将 Blob 添加到 Azure 数字孪生中的对象。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 36f4caac38f2f4891af6f61b78b55c7eff15eae4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116732"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>将 Blob 添加到 Azure 数字孪生中的对象

Blob 是常见文件类型（例如图片和日志）的非结构化表示形式。 Blob 使用 MIME 类型（例如“image/jpeg”）和元数据（名称、说明、类型等）来跟踪它们表示的数据类型。

Azure 数字孪生支持将 Blob 附加到设备、空间和用户。 Blob 可以表示用户个人资料图片、设备照片、视频、地图、固件 zip、JSON 数据、日志，等等。

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>上传 Blob：概述

可以使用多部分请求将 Blob 上传到特定的终结点及其相应功能。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob 元数据

除了 **Content-Type** 和 **Content-Disposition** 之外，Azure 数字孪生 blob 多部分请求还必须指定正确的 JSON 正文。 要提交哪个 JSON 正文取决于执行的 HTTP 请求操作的类型。

四个主要 JSON 架构是：

![JSON 架构][1]

Swagger 文档完整详细地介绍了这些模型架构。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

请阅读[如何使用 Swagger](./how-to-use-swagger.md) 了解如何使用参考文档。

### <a name="examples"></a>示例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

若要将文本文件上传为 blob 并将其与某个空间相关联，请向以下项发出经身份验证的 HTTP POST 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

使用以下正文：

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| 值 | 替换为 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 多部分内容边界名称 |

下面的代码使用 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 类完成相同 Blob 上传操作的 .NET 实现：

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

在这两个示例中：

1. 验证标头是否包括：`Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`。
1. 验证正文由多个部分组成：

   - 第一部分包含所需的 blob 元数据。
   - 第二部分包含文本文件。

1. 验证该文本文件提供为 `Content-Type: text/plain`。

## <a name="api-endpoints"></a>API 终结点

以下部分介绍与核心 blob 相关的 API 终结点及其功能。

### <a name="devices"></a>设备

可以将 blob 附加到设备。 下图显示了管理 API 的 Swagger 参考文档。 其中指定了使用 Blob 时所需的设备相关 API 终结点，以及要传入其中的所有必需路径参数。

![设备 Blob][2]

例如，若要更新或创建某个 Blob 并将其附加到设备，请向以下项发出经身份验证的 HTTP PATCH 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

成功的请求将在响应中返回 DeviceBlob JSON 对象。 DeviceBlob 对象符合以下 JSON 架构：

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **DeviceBlobType** | String | 可附加到设备的 Blob 类别 | `Model` 和 `Specification` |
| **DeviceBlobSubtype** | String | 比 DeviceBlobType 更明确的 Blob 子类别 | `PhysicalModel`、`LogicalModel`、`KitSpecification` 和 `FunctionalSpecification` |

> [!TIP]
> 使用上表处理成功返回的请求数据。

### <a name="spaces"></a>空间

此外，还可以将 blob 附加到空间。 下图列出了负责处理 Blob 的所有空间 API 终结点。 此外，它还列出了传入这些终结点的所有路径参数。

![空间 Blob][3]

例如，若要返回附加到某个空间的 Blob，请向以下项发出经身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

对同一终结点发出的 PATCH 请求将更新元数据说明并创建 blob 的新版本。 HTTP 请求是通过 PATCH 方法以及任何所需的元和多部分表单数据发出的。

成功的操作将返回符合以下架构的 SpaceBlob 对象。 可以使用此对象利用返回的数据。

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | String | 可附加到空间的 Blob 类别 | `Map` 和 `Image` |
| **SpaceBlobSubtype** | String | 比 SpaceBlobType 更明确的 Blob 子类别 | `GenericMap`、`ElectricalMap`、`SatelliteMap` 和 `WayfindingMap` |

### <a name="users"></a>用户

可将 Blob 附加到用户模型（例如，关联个人资料图片）。 下图显示了相关的用户 API 终结点和所有必需的路径参数（例如 `id`）：

![用户 Blob][4]

例如，若要提取附加到某个用户的 Blob，请向以下项发出包含所有必需表单数据的经身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

返回的 JSON（UserBlob 对象）符合以下 JSON 模型：

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **UserBlobType** | String | 可附加到用户的 Blob 类别 | `Image` 和 `Video` |
| **UserBlobSubtype** |  String | 比 UserBlobType 更明确的 Blob 子类别 | `ProfessionalImage`、`VacationImage` 和 `CommercialVideo` |

## <a name="common-errors"></a>常见错误

一种常见错误为未包含正确的标头信息：

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 数字孪生 Swagger 参考文档，请阅读[使用数字孪生 Swagger](how-to-use-swagger.md)。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
