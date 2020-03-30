---
title: 使用视频索引器 API 自定义语言模型
titlesuffix: Azure Media Services
description: 了解如何使用视频索引器 API 自定义语言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127980"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>使用视频索引器 API 自定义语言模型

视频索引器可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可按本主题所述，使用视频索引器 API 在帐户中创建和编辑自定义语言模型。 也可以按[使用视频索引器网站自定义语言模型](customize-language-model-with-api.md)中所述使用网站。

## <a name="create-a-language-model"></a>创建语言模型

[创建语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?)API 在指定的帐户中创建新的自定义语言模型。 可以在此调用中上传语言模型的文件。 或者，可以在此处创建语言模型，稍后再通过更新语言模型上传模型的文件。

> [!NOTE]
> 仍必须使用模型的已启用文件来训练该模型，以学习其文件的内容。 下一部分提供了有关训练语言的指导。

要上载要添加到语言模型的文件，除了提供上述所需参数的值外，还必须使用 FormData 在正文中上载文件。 执行此任务的方法有两种：

* 键将是文件名，值将是 txt 文件。
* 密钥将是文件名，值将是 txt 文件的 URL。

### <a name="response"></a>响应

响应提供新创建的语言模型上的元数据，以及以下示例 JSON 输出的格式的每个模型文件的元数据：

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

[训练语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)API 在指定帐户中训练自定义语言模型，其中的内容在语言模型中上载并启用。

> [!NOTE]
> 必须先创建语言模型并上传其文件。 您可以在创建语言模型或更新语言模型时上载文件。

### <a name="response"></a>响应

响应提供新培训的语言模型上的元数据，以及以下示例 JSON 输出的格式的每个模型文件的元数据：

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

返回`id`的标识是用于区分语言模型的唯一 ID，同时`languageModelId`用于[将视频上载到索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)和[重新索引视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)API（也称为`linguisticModelId`视频索引器上载/重新索引 API）。

## <a name="delete-a-language-model"></a>删除语言模型

[删除语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)API 将从指定的帐户中删除自定义语言模型。 使用已删除的语言模型的任何视频都将保留相同的索引，直到重新索引视频。 如果重新对视频进行索引，则可以为视频分配新的语言模型。 否则，视频索引器将使用其默认模型重新索引视频。

### <a name="response"></a>响应

成功删除语言模型时，没有返回的内容。

## <a name="update-a-language-model"></a>更新语言模型

[更新语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)API 更新指定帐户中的自定义语言人员模型。

> [!NOTE]
> 必须事先创建一个语言模型。 可以使用此调用来启用或禁用模型下的所有文件、更新语言模型的名称，以及上传要添加到语言模型的文件。

要上载要添加到语言模型的文件，除了提供上述所需参数的值外，还必须使用 FormData 在正文中上载文件。 执行此任务的方法有两种：

* 键将是文件名，值将是 txt 文件。
* 密钥将是文件名，值将是 txt 文件的 URL。

### <a name="response"></a>响应

响应提供新培训的语言模型上的元数据，以及以下示例 JSON 输出的格式的每个模型文件的元数据：

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

`id`使用响应中返回的文件下载文件的内容。

## <a name="update-a-file-from-a-language-model"></a>更新语言模型中的文件

文件[的更新](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update)允许您更新指定帐户中自定义语言模型中的文件`enable`的名称和状态。

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

`id`使用响应中返回的文件下载文件的内容。

## <a name="get-a-specific-language-model"></a>获取特定的语言模型

[get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API 返回指定帐户中指定语言模型的信息，如语言和语言模型中的文件。

### <a name="response"></a>响应

响应提供指定语言模型上的元数据，以及以下示例 JSON 输出的格式的每个模型文件的元数据：

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

`id`使用响应中返回的文件下载文件的内容。

## <a name="get-all-the-language-models"></a>获取所有语言模型

[获取所有](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get)API 返回列表中指定帐户中的所有自定义语言模型。

### <a name="response"></a>响应

响应提供帐户中所有语言模型的列表及其每个元数据和文件，遵循此示例 JSON 输出的格式：

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

[删除](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)API 从指定帐户中的指定语言模型中删除指定的文件。

### <a name="response"></a>响应

当文件从语言模型成功删除时，没有返回的内容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>获取有关语言模型中的文件的元数据

文件 API[的获取元数据](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model)从帐户中所选的语言模型返回指定文件的内容和元数据。

### <a name="response"></a>响应

响应以 JSON 格式提供文件的内容和元数据，类似于以下示例：

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

[下载文件](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?)API 从指定帐户中的指定语言模型中下载包含指定文件内容的文本文件。 此文本文件应与最初上传的文本文件的内容相匹配。

### <a name="response"></a>响应

响应是下载的文本文件，其中包含该文件的 JSON 格式内容。

## <a name="next-steps"></a>后续步骤

[使用网站自定义语言模型](customize-language-model-with-website.md)
