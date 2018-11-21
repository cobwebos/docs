---
title: 如何将 Blob 添加到 Azure 数字孪生中的对象 | Microsoft Docs
description: 了解如何将 Blob 添加到 Azure 数字孪生中的对象
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688309"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>如何将 Blob 添加到 Azure 数字孪生中的对象

Blob 是常见文件类型（例如图片和日志）的非结构化表示形式。 Blob 使用 MIME 类型（例如“image/jpeg”）和元数据（名称、说明、类型等）来跟踪它们表示的数据类型。

Azure 数字孪生支持将 Blob 附加到设备、空间和用户。 Blob 可以表示用户个人资料图片、设备照片、视频、地图或日志。

> [!NOTE]
> 本文假设：
> * 实例已正确配置为接收管理 API 请求。
> * 已使用所选的 REST 客户端正确完成身份验证。

## <a name="uploading-blobs-an-overview"></a>上传 Blob：概述

使用多部分请求将 Blob 上传到特定的终结点及其相应功能。

> [!IMPORTANT]
> 多部分请求需要三个必不可少的信息片段。 对于 Azure 数字孪生：
> * **Content-Type** 标头：
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`。
> * **Content-Disposition**：`form-data; name="metadata"`。
> * 要上传的文件内容。
>
> 确切的 **Content-Type** 和 **Content-Disposition** 根据使用方案的不同而异。

每个多部分请求划分为若干个区块。 向 Azure 数字孪生管理 API 发出的多部分请求划分成下面所述的**两个** (**2**) 个部分：

1. 第一个部分是必需的，其中包含 Blob 元数据，例如，上述每个 **Content-Type** 和 **Content-Disposition** 的关联 MIME 类型。

1. 第二个部分包含实际的 Blob 内容（文件的非结构化内容）。  

对于 **PATCH** 请求，上述两个部分都不是必需的。 对于 **POST** 请求或 create 操作，两者都是必需的。

### <a name="blob-metadata"></a>Blob 元数据

除了 **Content-Type** 和 **Content-Disposition** 以外，多部分请求还必须指定正确的 JSON 正文。 要提交哪个 JSON 正文取决于执行的 HTTP 请求操作的类型。

使用的四个主要 JSON 架构是：

![空间 Blob][1]

随附的 Swagger 文档中完整详细地介绍了这些模型架构。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

请阅读[如何使用 Swagger](./how-to-use-swagger.md) 了解如何使用随附的参考文档。

### <a name="examples"></a>示例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

若要发出以 Blob 形式上传文本文件的 POST 请求并将它与某个空间相关联：

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| 参数值 | 替换为 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | 多部分内容边界名称 |

下面使用 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 类提供了相同 Blob 上传操作的 .NET 实现：

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API 终结点

下面提供了核心终结点及其特定功能的演练。

### <a name="devices"></a>设备

可将 Blob 附加到设备。 下图（描绘管理 API 的 Swagger 参考文档）指定了使用 Blob 时所需的设备相关 API 终结点，以及要传入其中的所有必需路径参数：

![设备 Blob][2]

例如，为了更新或创建某个 Blob 并将其附加到设备，发出了以下 PATCH 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

成功的请求将在响应中返回 DeviceBlob JSON 对象。 DeviceBlobs 符合以下 JSON 架构：

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **DeviceBlobType** | String | 可附加到设备的 Blob 类别 | `Model` 和 `Specification` |
| **DeviceBlobSubtype*** | String | 比 DeviceBlobType 粒度更高的 Blob 子类别 | `PhysicalModel`、`LogicalModel`、`KitSpecification` 和 `FunctionalSpecification` |

> [!TIP]
> 使用上表处理成功返回的请求数据。

### <a name="spaces"></a>空格

还可将 Blob 附加到空间。 下图列出了负责处理 Blob 以及传入其中的所有路径参数的所有空间 API 终结点：

![空间 Blob][3]

例如，为了返回附加到某个空间的 Blob，发出了以下 GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

向同一终结点发出 PATCH 请求可以更新元数据说明并创建 Blob 的新版本。 HTTP 请求是使用 PATCH 方法以及任何所需的元和多部分表单数据发出的。

成功的操作将返回符合以下架构并可用于使用返回的数据的 SpaceBlob：

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **SpaceBlobType** | String | 可附加到空间的 Blob 类别 | `Map` 和 `Image` |
| **SpaceBlobSubtype** | String | 比 SpaceBlobType 粒度更高的 Blob 子类别 | `GenericMap`、`ElectricalMap`、`SatelliteMap` 和 `WayfindingMap` |

### <a name="users"></a>用户

可将 Blob 附加到用户模型（例如，关联个人资料图片）。 下图描绘了相关的用户 API 终结点和所有必需的路径参数（例如 `id`）：

![用户 Blob][4]

例如，若要提取附加到某个用户的 Blob，可发出以下包含所有必需表单数据的 GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

返回的 JSON (UserBlobs) 符合以下 JSON 模型：

| 属性 | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **UserBlobType** | String | 可附加到用户的 Blob 类别 | `Image` 和 `Video` |
| **UserBlobSubtype** |  String | 比 UserBlobType 粒度更高的 Blob 子类别 | `ProfessionalImage`、`VacationImage` 和 `CommercialVideo` |

## <a name="common-errors"></a>常见错误

不包含正确的标头信息：

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>后续步骤

若要详细了解随附的 Azure 数字孪生 Swagger 参考文档，请阅读[如何使用数字孪生 Swagger](how-to-use-swagger.md)。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
