---
title: Microsoft 语音翻译 API 语言方法 | Microsoft Docs
titleSuffix: Cognitive Services
description: 使用 Microsoft 语音翻译 API 语言方法。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 5396e3be17345c3c36197a9b6cbace86e1f574c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366834"
---
# <a name="speech-api-languages"></a>语音 API：语言

Microsoft Translator 不断扩展其服务支持的语言列表。 使用此 API 可以发现当前可用于语音翻译服务的语言集。

可以从 [Microsoft Translator Github 站点](https://github.com/MicrosoftTranslator)获得演示如何使用 API 获取可用语言的代码示例。

## <a name="implementation-notes"></a>实现说明

GET /语言 

有多种语言可用于转录语音、翻译转录文本，以及生成翻译的合成语音。

客户端使用 `scope` 查询参数来定义它关注的语言集。

* **语音转文本：** 使用查询参数 `scope=speech` 检索可用于将语音转录为文本的语言集。
* **文本翻译：** 使用查询参数 `scope=text` 检索可用于翻译转录文本的语言集。
* **文本转语音：** 使用查询参数 `scope=tts` 检索可用于将翻译后的文本合成回语音的语言和语音集。

客户端可以通过指定以逗号分隔的选项列表来同时检索多个集。 例如，`scope=speech,text,tts`。

成功的响应是一个 JSON 对象，其中包含请求的每个集的一个属性。

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

由于客户端可以使用 `scope` 查询参数来选择应返回哪些受支持的语言集，因此实际响应可能仅包括上面显示的所有属性的子集。

每个属性提供的值如下所示。

### <a name="speech-to-text-speech"></a>语音转文本（语音）

与语音转文本属性 `speech` 关联的值是 (键, 值) 对的字典。 每个键标识支持语音转文本的语言。 键是客户端传递给 API 的标识符。 与键关联的值是具有以下属性的对象：

* `name`：语言的显示名称。
* `language`：相关书面语言的语言标记。 请参阅下面的“文本翻译”。
示例如下：

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>文本翻译（文本）

与 `text` 属性关联的值也是字典，其中每个键标识文本翻译支持的语言。 与键关联的值描述语言：

* `name`：语言的显示名称。
* `dir`：方向性，`rtl` 表示语言从右向左，`ltr` 表示语言从左到右。

示例如下：

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>文本转语音 (tts)

与文本转语音属性 tts 关联的值也是字典，其中每个键标识支持的语音。 语音对象的属性如下：

* `displayName`：语音的显示名称。
* `gender`：语音的性别（男性或女性）。
* `locale`：语音的语言标记，其中包含主语言子标记和区域子标记。
* `language`：相关书面语言的语言标记。
* `languageName`：语言的显示名称。
* `regionName`：此语言的区域的显示名称。

示例如下：

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>本地化
对于文本翻译支持的所有语言，该服务将返回“Accept-Language”标头的语言中的所有名称。

### <a name="response-class-status-200"></a>响应类（状态 200）
描述受支持语言集的对象。

ModelExample 值： 

Langagues { speech (object, optional), text (object, optional), tts (object, optional) }

### <a name="headers"></a>标头

|标头|说明|Type|
:--|:--|:--|
X-RequestId|服务器生成的值，用于标识请求并用于故障排除目的。|字符串|

### <a name="parameters"></a>parameters

|参数|说明|参数类型|数据类型|
|:--|:--|:--|:--|
|api-version    |客户端请求的 API 版本。 允许值包括：`1.0`。|query|字符串|
|作用域  |要返回给客户端的受支持语言或语音集。 此参数指定为以逗号分隔的关键字列表。 以下关键字可用：<ul><li>`speech`：提供支持转录语音的语言集。</li><li>`tts`：提供支持文本-语音转换的语音集。</li><li>`text`：提供支持翻译文本的语言集。</li></ul>如果未指定值，则 `scope` 的值默认为 `text`。|query|字符串|
|X-ClientTraceId    |客户端生成的 GUID，用于跟踪请求。 为了便于对问题进行故障排除，客户端应为每个请求提供新值并记录该值。|标头的值开始缓存响应|字符串|
|Accept-Language    |响应中的某些字段是语言或区域的名称。 使用此参数可以定义要以哪种语言返回名称。 通过提供格式正确的 BCP 47 语言标记来指定语言。 从与 `text` 范围一起返回的语言标识符列表中选择一个标记。 对于不受支持的语言，名称以英语提供。<br/>例如，使用值 `fr` 来请求法语名称，或使用值 `zh-Hant` 来请求繁体中文名称。|标头的值开始缓存响应|字符串|
    
### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|
|:--|:--|
|400|错误的请求。 请检查输入参数以确保它们有效。 响应对象包括错误的更详细描述。|
|429|请求过多。|
|500|出现错误。 如果错误仍然存在，请使用客户端跟踪标识符 (X-ClientTraceId) 或请求标识符 (X-RequestId) 进行报告。|
|503|服务器暂不可用。 请重试请求。 如果错误仍然存在，请使用客户端跟踪标识符 (X-ClientTraceId) 或请求标识符 (X-RequestId) 进行报告。|
