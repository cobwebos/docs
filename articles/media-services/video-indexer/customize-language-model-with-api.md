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
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047086"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>使用视频索引器 API 自定义语言模型

视频索引器可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可按本主题所述，使用视频索引器 API 在帐户中创建和编辑自定义语言模型。 也可以按[使用视频索引器网站自定义语言模型](customize-language-model-with-api.md)中所述使用网站。

## <a name="create-a-language-model"></a>创建语言模型

[创建语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?)API 将在指定的帐户中创建一个新的自定义语言模型。 可以在此调用中上传语言模型的文件。 或者，可以在此处创建语言模型，稍后再通过更新语言模型上传模型的文件。

> [!NOTE]
> 仍必须使用模型的已启用文件来训练该模型，以学习其文件的内容。 下一部分提供了有关训练语言的指导。

若要上传要添加到语言模型中的文件，必须在正文中使用 FormData 上传文件，并为上述必需的参数提供值。 可以通过两种方法执行此任务：

* Key 将为文件名，而值将是 txt 文件。
* Key 将为文件名，而值将是指向 txt 文件的 URL。

### <a name="response"></a>响应

响应按照此示例 JSON 输出的格式，在新创建的语言模型和每个模型文件上的元数据中提供元数据：

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

[训练语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)API 使用上传到并在语言模型中启用的文件中的内容训练指定帐户中的自定义语言模型。

> [!NOTE]
> 必须先创建语言模型并上传其文件。 您可以在创建语言模型或更新语言模型时上载文件。

### <a name="response"></a>响应

响应按照此示例 JSON 输出的格式，在新训练的语言模型和每个模型文件上的元数据中提供元数据：

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

返回的 `id` 是用于区分语言模型的唯一 ID，同时 `languageModelId` 用于 [上传视频来索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 和重新索引 [视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) Api， (也称为 `linguisticModelId` 视频索引器上传/重新索引 api) 。

## <a name="delete-a-language-model"></a>删除语言模型

[删除语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)API 将从指定的帐户中删除自定义语言模型。 使用已删除的语言模型的任何视频都将保留相同的索引，直到对视频重新建立索引。 如果对视频重新编制索引，则可以将新的语言模型分配给视频。 否则，视频索引器将使用其默认模型来重新索引视频。

### <a name="response"></a>响应

成功删除语言模型后，不会返回任何内容。

## <a name="update-a-language-model"></a>更新语言模型

[更新语言模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)API 会更新指定帐户中的自定义语言用户模型。

> [!NOTE]
> 必须事先创建一个语言模型。 可以使用此调用来启用或禁用模型下的所有文件、更新语言模型的名称，以及上传要添加到语言模型的文件。

若要上传要添加到语言模型中的文件，必须在正文中使用 FormData 上传文件，并为上述必需的参数提供值。 可以通过两种方法执行此任务：

* Key 将为文件名，而值将是 txt 文件。
* Key 将为文件名，而值将是指向 txt 文件的 URL。

### <a name="response"></a>响应

响应按照此示例 JSON 输出的格式，在新训练的语言模型和每个模型文件上的元数据中提供元数据：

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

使用 `id` 响应中返回的文件下载文件的内容。

## <a name="update-a-file-from-a-language-model"></a>更新语言模型中的文件

使用 [更新文件](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) 可以更新 `enable` 指定帐户的自定义语言模型中的文件的名称和状态。

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

使用 `id` 在响应中返回的文件的来下载文件的内容。

## <a name="get-a-specific-language-model"></a>获取特定的语言模型

[Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API 返回指定帐户中指定语言模型的信息，如语言和语言模型中的文件。

### <a name="response"></a>响应

响应按照此示例 JSON 输出的格式，在指定的语言模型和每个模型文件上的元数据中提供元数据：

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

使用 `id` 在响应中返回的文件的来下载文件的内容。

## <a name="get-all-the-language-models"></a>获取所有语言模型

" [获取所有](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API" 返回列表中指定帐户的所有自定义语言模型。

### <a name="response"></a>响应

响应提供了你的帐户中所有语言模型的列表及其每个语言模型的列表，该列表采用以下示例 JSON 输出格式：

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

[删除](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)API 将从指定帐户中的指定语言模型删除指定的文件。

### <a name="response"></a>响应

成功从语言模型删除文件时，不会返回任何内容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>获取有关语言模型中的文件的元数据

文件 API 的 [get 元数据](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) 从你的帐户中所选的语言模型返回指定文件上的内容和元数据。

### <a name="response"></a>响应

响应提供 JSON 格式的文件内容和元数据，类似于以下示例：

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

" [下载文件](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API" 下载一个文本文件，其中包含指定帐户中指定语言模型的指定文件的内容。 此文本文件应与最初上传的文本文件的内容相匹配。

### <a name="response"></a>响应

响应是下载的文本文件，其中包含该文件的 JSON 格式内容。

## <a name="next-steps"></a>后续步骤

[使用网站自定义语言模型](customize-language-model-with-website.md)
