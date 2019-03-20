---
title: 使用视频索引器 API 自定义语言模型 - Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器 API 自定义语言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: c2c722331c95e72bae5605606564a2083e2802e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075026"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>使用视频索引器 API 自定义语言模型

视频索引器可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。 

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可按本主题所述，使用视频索引器 API 在帐户中创建和编辑自定义语言模型。 也可以按[使用视频索引器网站自定义语言模型](customize-language-model-with-api.md)中所述使用网站。

## <a name="create-a-language-model"></a>创建语言模型

以下命令在指定的帐户中创建新的自定义语言模型。 可以在此调用中上传语言模型的文件。 或者，可以在此处创建语言模型，稍后再通过更新语言模型上传模型的文件。

> [!NOTE]
> 仍必须使用模型的已启用文件来训练该模型，以学习其文件的内容。 下一部分提供了有关训练语言的指导。

### <a name="request-url"></a>请求 URL

这是一个 POST 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?)。

### <a name="request-parameters"></a>请求参数

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|
|modelName|字符串|是|语言模型的名称|
|语言|字符串|是|语言模型的语言。 <br/>必须以“语言标记-区域”BCP-47 格式（例如“en-US”）为 **language** 参数指定语言。 支持的语言包括英语 (en-US)、德语 (de-DE)、西班牙语 (es-SP)、阿拉伯语 (ar-EG)、法语 (fr-FR)、印地语 (hi-HI)、意大利语 (it-IT)、日语 (ja-JP)、葡萄牙语 (pt-BR)、俄语 (ru-RU) 和中文 (zh-CN)。  |

### <a name="request-body"></a>请求正文

若要上传要添加到语言模型的文件，必须使用表单数据在正文中上传文件，此外，必须为上述所需参数提供值。 可通过两种方式实现此目的： 

1. 密钥是文件名，值是 txt 文件
2. 密钥是文件名，值是 txt 文件的 URL

### <a name="response"></a>响应

响应提供有关新建的语言模型的元数据，以及有关每个模型的文件的元数据（遵循示例 JSON 输出的格式）。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>训练语言模型

以下命令使用已在语言模型中上传并启用的文件中的内容，在指定的帐户中训练一个自定义的语言模型。 

> [!NOTE]
> 必须先创建语言模型并上传其文件。 可以在创建语言模型时或者通过更新语言模型来上传文件。 

### <a name="request-url"></a>请求 URL

这是一个 PUT 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?)。

### <a name="request-parameters"></a>请求参数

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|语言模型 ID（创建语言模型时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供有关新训练的语言模型的元数据，以及有关每个模型的文件的元数据（遵循示例 JSON 输出的格式）。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

然后，应在[将视频上传到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)时，对 **linguisticModelId** 参数使用语言模型的 **id** 值，并在[重建编制视频的索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)时，对 **languageModelId** 参数使用该值。

## <a name="delete-a-language-model"></a>删除语言模型

以下命令从指定的帐户中删除自定义语言模型。 使用已删除语言模型的任何视频会保留相同的索引，直到为该视频重新编制索引为止。 如果重新为视频编制索引，可为视频分配新的语言模型。 否则，视频索引器会使用其默认模型重新为视频编制索引。

### <a name="request-url"></a>请求 URL

这是一个 DELETE 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|语言模型 ID（创建语言模型时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

成功删除语言模型后不会返回内容。

## <a name="update-a-language-model"></a>更新语言模型

以下命令在指定的帐户中更新自定义语言个人模型。

> [!NOTE]
> 必须事先创建一个语言模型。 可以使用此调用来启用或禁用模型下的所有文件、更新语言模型的名称，以及上传要添加到语言模型的文件。

### <a name="request-url"></a>请求 URL

这是一个 PUT 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

下面是 Curl 中的请求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|语言模型 ID（创建语言模型时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|
|modelName|字符串|否|可为模型指定的新名称|
|enable|布尔值|否|选择此模型下的所有文件是已启用 (true) 还是已禁用 (false)|

### <a name="request-body"></a>请求正文

若要上传要添加到语言模型的文件，必须使用表单数据在正文中上传文件，此外，必须为上述所需参数提供值。 可通过两种方式实现此目的： 

1. 密钥是文件名，值是 txt 文件
2. 密钥是文件名，值是 txt 文件的 URL

### <a name="response"></a>响应

响应提供有关新训练的语言模型的元数据，以及有关每个模型的文件的元数据（遵循示例 JSON 输出的格式）。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
可以使用此处返回的文件 **id** 来下载文件的内容。

## <a name="update-a-file-from-a-language-model"></a>更新语言模型中的文件

以下命令用于更新指定帐户中某个自定义语言模型内的某个文件的名称和 **enable** 状态。

### <a name="request-url"></a>请求 URL

这是一个 PUT 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

下面是 Curl 中的请求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountId|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|保存文件的语言模型的 ID（创建语言模型时生成）|
|fileId|字符串|是|正在更新的文件的 ID（在创建或更新语言模型的过程中上传文件时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|
|fileName|字符串|否|将文件名更新后的名称|
|enable|布尔值|否|在语言模型中更新此文件的已启用 (true) 或已禁用 (false) 状态|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供有关已更新的文件的元数据（遵循以下示例 JSON 输出的格式）。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
可以使用此处返回的文件 **id** 来下载文件的内容。

## <a name="get-a-specific-language-model"></a>获取特定的语言模型

以下命令返回有关指定帐户指定语言模型的信息，例如语言以及语言模型中的文件。 

### <a name="request-url"></a>请求 URL

这是一个 GET 请求。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16)。

### <a name="request-parameters-and-request-body"></a>请求参数和请求正文

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|语言模型 ID（创建语言模型时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供有关指定的语言模型的元数据，以及有关每个模型的文件的元数据（遵循以下示例 JSON 输出的格式）。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

可以使用此处返回的文件 **id** 来下载文件的内容。

## <a name="get-all-the-language-models"></a>获取所有语言模型

以下命令在列表中返回指定帐户中的所有自定义语言模型。

### <a name="request-url"></a>请求 URL

这是一个 GET 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?)。

### <a name="request-parameters"></a>请求参数

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供一个列表，其中包含帐户中的所有语言模型，以及这些模型的每个元数据和文件（遵循以下示例 JSON 输出的格式）。

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>从语言模型中删除文件

以下命令从指定帐户中的指定语言模型删除指定的文件。 

### <a name="request-url"></a>请求 URL

这是一个 DELETE 请求。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|保存文件的语言模型的 ID（创建语言模型时生成）|
|fileId|字符串|是|正在更新的文件的 ID（在创建或更新语言模型的过程中上传文件时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

成功从语言模型中删除文件后不会返回内容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>获取有关语言模型中的文件的元数据

此命令返回帐户中所选语言模型内的指定文件的内容及其相关元数据。

### <a name="request-url"></a>请求 URL

这是一个 GET 请求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

下面是 Curl 中的请求。
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|保存文件的语言模型的 ID（创建语言模型时生成）|
|fileId|字符串|是|正在更新的文件的 ID（在创建或更新语言模型的过程中上传文件时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应提供文件的 JSON 格式内容和元数据，如下所示：

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 此示例文件的内容是分两行显示的单词“hello”和“world”。

## <a name="download-a-file-from-a-language-model"></a>从语言模型下载文件

以下命令从指定帐户中的指定语言模型下载包含指定文件内容的文本文件。 此文本文件应与最初上传的文本文件的内容相匹配。

### <a name="request-url"></a>请求 URL
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

下面是 Curl 中的请求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[使用视频索引器开发人员门户查看所需的参数并进行测试](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122)。

### <a name="request-parameters"></a>请求参数 

|**名称**|类型|**必需**|**说明**|
|---|---|---|---|
|位置|字符串|是|应将调用路由到的 Azure 区域。 有关详细信息，请参阅 [Azure 区域和视频索引器](regions.md)。|
|accountID|字符串|是|帐户的全局唯一标识符|
|modelId|字符串|是|保存文件的语言模型的 ID（创建语言模型时生成）|
|fileId|字符串|是|正在更新的文件的 ID（在创建或更新语言模型的过程中上传文件时生成）|
|accessToken|字符串|是|用于针对调用进行身份验证的访问令牌（必须是[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)范围）。 访问令牌在 1 小时内过期。|

### <a name="request-body"></a>请求正文 

此调用不需要更多的请求正文。

### <a name="response"></a>响应

响应是下载的文本文件，其中包含该文件的 JSON 格式内容。 

## <a name="next-steps"></a>后续步骤

[使用网站自定义语言模型](customize-language-model-with-website.md)
