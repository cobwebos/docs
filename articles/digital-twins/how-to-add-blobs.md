---
title: 如何将 blob 添加到对象-Azure 数字孪生 |Microsoft Docs
description: 了解如何在 Azure 数字孪生中向用户、设备和空间添加 blob。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6ab9d0ae07978e69bebb0fc24c8965cce971cfd5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082343"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>将 Blob 添加到 Azure 数字孪生中的对象

Blob 是常见文件类型（例如图片和日志）的非结构化表示形式。 Blob 使用 MIME 类型（例如“image/jpeg”）和元数据（名称、说明、类型等）来跟踪它们表示的数据类型。

Azure 数字孪生支持将 Blob 附加到设备、空间和用户。 Blob 可以表示用户个人资料图片、设备照片、视频、地图、固件 zip、JSON 数据、日志，等等。

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>上传 Blob 概述

可以使用多部分请求将 Blob 上传到特定的终结点及其相应功能。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob 元数据

除了 **Content-Type** 和 **Content-Disposition** 之外，Azure 数字孪生 blob 多部分请求还必须指定正确的 JSON 正文。 要提交哪个 JSON 正文取决于执行的 HTTP 请求操作的类型。

四个主要 JSON 架构是：

[![JSON 架构](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

JSON Blob 元数据符合以下模型：

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| **parentId** | String | 要与 Blob 关联的父实体（空间、设备或用户） |
| **名称** |String | Blob 的用户友好名称 |
| **类型** | String | Blob 的类型 - 不能使用 *type* 和 *typeId*  |
| **typeId** | Integer | Blob 类型 ID - 不能使用 *type* 和 *typeId* |
| **subtype** | String | Blob 子类型 - 不能使用 *subtype* 和 *subtypeId* |
| **subtypeId** | Integer | Blob 的子类型 ID - 不能使用 *subtype* 和 *subtypeId* |
| **说明** | String | Blob 的自定义说明 |
| **sharing** | String | 是否可以共享 Blob - enum [`None`, `Tree`, `Global`] |

Blob 元数据始终提供为具有 **Content-Type** `application/json` 的第一个区块，或提供为 `.json` 文件。 文件数据在第二个区块中提供，可以是任何受支持的 MIME 类型。

Swagger 文档完整详细地介绍了这些模型架构。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

请阅读[如何使用 Swagger](./how-to-use-swagger.md) 了解如何使用参考文档。

### <a name="blobs-response-data"></a>Blob 响应数据

单独返回的 Blob 符合以下 JSON 架构：

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| **id** | String | Blob 的唯一标识符 |
| **名称** |String | Blob 的用户友好名称 |
| **parentId** | String | 要与 Blob 关联的父实体（空间、设备或用户） |
| **类型** | String | Blob 的类型 - 不能使用 *type* 和 *typeId*  |
| **typeId** | Integer | Blob 类型 ID - 不能使用 *type* 和 *typeId* |
| **subtype** | String | Blob 子类型 - 不能使用 *subtype* 和 *subtypeId* |
| **subtypeId** | Integer | Blob 的子类型 ID - 不能使用 *subtype* 和 *subtypeId* |
| **sharing** | String | 是否可以共享 Blob - enum [`None`, `Tree`, `Global`] |
| **说明** | String | Blob 的自定义说明 |
| **contentInfos** | Array | 指定包括版本的非结构化元数据信息 |
| **fullName** | String | Blob 的全名 |
| **spacePaths** | String | 空间路径 |

Blob 元数据始终提供为具有 **Content-Type** `application/json` 的第一个区块，或提供为 `.json` 文件。 文件数据在第二个区块中提供，可以是任何受支持的 MIME 类型。

### <a name="blob-multipart-request-examples"></a>Blob 多部分请求示例

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

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

最后，[cURL](https://curl.haxx.se/) 用户可以相同的方式发出多部分表单请求：

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| 值 | 替换为 |
| --- | --- |
| YOUR_TOKEN | 有效的 OAuth 2.0 令牌 |
| YOUR_SPACE_ID | 要与 Blob 关联的空间的 ID |
| PATH_TO_FILE | 文本文件的路径 |

[![卷曲示例](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

成功的 POST 返回新 blob 的 ID。

## <a name="api-endpoints"></a>API 终结点

以下部分介绍与核心 blob 相关的 API 终结点及其功能。

### <a name="devices"></a>设备

可以将 blob 附加到设备。 下图显示了管理 API 的 Swagger 参考文档。 其中指定了使用 Blob 时所需的设备相关 API 终结点，以及要传入其中的所有必需路径参数。

[![设备 blob](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

例如，若要更新或创建某个 Blob 并将其附加到设备，请向以下项发出经身份验证的 HTTP PATCH 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

成功的请求返回[如前所述](#blobs-response-data)的 JSON 对象。

### <a name="spaces"></a>空格

此外，还可以将 blob 附加到空间。 下图列出了负责处理 Blob 的所有空间 API 终结点。 此外，它还列出了传入这些终结点的所有路径参数。

[![空间 blob](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

例如，若要返回附加到某个空间的 Blob，请向以下项发出经身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

成功的请求返回[如前所述](#blobs-response-data)的 JSON 对象。

对同一个终结点发出 PATCH 请求会更新元数据说明并创建 Blob 的版本。 HTTP 请求是通过 PATCH 方法以及任何所需的元和多部分表单数据发出的。

### <a name="users"></a>用户

可将 Blob 附加到用户模型（例如，关联个人资料图片）。 下图显示了相关的用户 API 终结点和所有必需的路径参数（例如 `id`）：

[![用户 blob](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

例如，若要提取附加到某个用户的 Blob，请向以下项发出包含所有必需表单数据的经身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_BLOB_ID* | 所需的 Blob ID |

成功的请求返回[如前所述](#blobs-response-data)的 JSON 对象。

## <a name="common-errors"></a>常见错误

一种常见错误的原因是未提供正确的标头信息：

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

若要解决此错误，请检查整个请求是否包含相应的 **Content-Type** 标头：

* `multipart/mixed`
* `multipart/form-data`

此外，检查每个多部分区块是否包含所需的相应 **Content-Type**。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 数字孪生 Swagger 参考文档，请阅读[使用数字孪生 Swagger](how-to-use-swagger.md)。

- 若要通过 Postman 上传 Blob，请参阅[如何配置 Postman](./how-to-configure-postman.md)。