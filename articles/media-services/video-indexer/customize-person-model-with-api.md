---
title: 使用视频索引器 API 自定义人员模型 - Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器 API 自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360533"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>使用视频索引器 API 自定义人员模型

视频索引器支持视频内容的人脸检测和名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 会检测到名人识别功能未识别的人脸；但是，这些人脸会保留未命名状态。 将视频上传到视频索引器并获得结果后，即可回过头来命名未识别的人脸。 使用名称标记人脸后，人脸和名称将添加到帐户的人员模型中。 然后，视频索引器会在将来的视频和过去的视频中识别出此人脸。

可以使用视频索引器 API 编辑视频中检测到的人脸，如本主题所述。 也可使用视频索引器网站，如[使用视频索引器网站自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多个人员模型 

视频索引器支持每个帐户使用多个人员模型。 目前只能通过视频索引器 API 使用此功能。

如果帐户需要面对不同的用例方案，则可能需要为每个帐户创建多个人员模型。 例如，如果内容与体育相关，则可为每个体育项目（橄榄球、篮球、足球等）创建单独的人员模型。 

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新的人脸时，会更新与该视频相关联的具体自定义模型。

每个帐户的限制为 50 个人员模型。 如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频索引器会使用帐户中的默认自定义人员模型。

## <a name="create-a-new-person-model"></a>创建新的人员模型

在指定帐户中创建新的人员模型。 

### <a name="request-url"></a>请求 URL

这是一个 POST 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)。

### <a name="request-parameters"></a>请求参数 

|**名称**|**Type**|**需要**|**描述**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|名称|字符串|是|人员模型的名称|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供刚刚按照以下示例的格式创建的人员模型的名称和生成的模型 ID。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

然后，在[上传视频到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)时，应该使用 **personModelId** 参数的 **id** 值。

## <a name="delete-a-person-model"></a>删除人员模型

从指定帐户删除自定义人员模型。 

成功删除人员模型以后，曾经使用已删除模型的当前视频的索引会保持不变，直至你重新对其进行索引。 重新索引后，视频索引器将无法在使用已删除模型进行索引的当前视频中识别在该模型中命名的人脸；但是，这些人脸仍然可以检测到。 使用已删除模型进行索引的当前视频现在会使用帐户的默认人员模型。 如果已删除模型中的人脸也在帐户的默认模型中命名，则可继续识别视频中的这些人脸。

### <a name="request-url"></a>请求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

下面是 Curl 中的请求。
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)。

### <a name="request-parameters"></a>请求参数

|**名称**|**Type**|**需要**|**描述**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|id|字符串|是|人员模型 ID（在创建人员模型时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

成功删除人员模型后没有返回的内容。

## <a name="get-all-person-models"></a>获取所有人员模型

获取指定帐户中的所有人员模型。 

### <a name="request-call"></a>请求调用

这是一个 GET 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)。

### <a name="request-parameters"></a>请求参数

|**名称**|**Type**|**需要**|**描述**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供一个列表，其中包含帐户中的所有人员模型（包括指定帐户中的默认人员模型），以及遵循以下示例格式的每个名称和 ID。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

可以在[上传视频到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或[重新索引视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)时使用人员模型的 **id** 值作为 **personModelId** 参数，以便选择需要用于视频的具体模型。

## <a name="update-a-face"></a>更新人脸

此命令允许将视频 ID 和人脸 ID 配合使用，以便通过名称来更新视频中的人脸。 这样一来，就可以在执行上传/索引或重新索引操作之后更新与视频相关联的人员模型。 如果未分配人员模型，则会更新帐户的默认人员模型。 

一旦出现这种情况，它会识别出现在其他当前视频中的同一人脸，这些视频共享同一人员模型。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。

可以使用新的名称来更新通过视频索引器识别为名人的人脸。 提供的新名称优先于内置的名人识别功能。

### <a name="request-call"></a>请求调用

这是一个 POST 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

下面是 Curl 中的请求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)。

### <a name="request-parameters"></a>请求参数

|**名称**|**Type**|**需要**|**描述**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|videoId|字符串|是|要更新的人脸所在视频的 ID。 此项是在上传视频并对其进行索引时创建的。|
|faceId|integer|是|要更新的人脸的 ID。 可以从视频索引中获取 faceId|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|
|名称|字符串|是|更新人脸时使用的新名称。|

名称特定于人员模型，因此如果为同一人员模型中的两个不同的人脸提供相同的 **name** 参数值，视频索引器会将这两个人脸视为同一人，并会在完成视频的重新索引后将其聚合在一起。 

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

成功更新人脸后没有返回的内容。

## <a name="next-steps"></a>后续步骤

[自定义个人模型使用视频索引器网站](customize-person-model-with-website.md)
