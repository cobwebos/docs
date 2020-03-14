---
title: 语音合成标记语言（SSML）-语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音合成标记语言来控制文本到语音转换中的发音和韵律。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: dapine
ms.openlocfilehash: 68691ad60542c55db4d381e2923a9f928a22995a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220497"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>通过语音合成标记语言（SSML）改善合成

语音合成标记语言（SSML）是一种基于 XML 的标记语言，它允许开发人员使用文本到语音服务来指定如何将输入文本转换为合成语音。 与纯文本相比，SSML 允许开发人员精细调整文本到语音输出的音调、发音、语速、音量和更多内容。 正常标点，如在一个句点后暂停，或使用正确的 intonation，当以问号结束的句子将自动处理。

SSML 的语音服务实现基于万维网联合会的[语音合成标记语言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、日语和朝鲜语字符作为两个字符进行计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>标准、神经和自定义语音

选择 "标准" 和 "神经" 语音，或创建自己的产品或品牌独有的自定义声音。 75多个标准语音在45多种语言和区域设置中提供，5个神经声音提供四种语言和区域设置。 有关支持的语言、区域设置和语音（神经和标准）的完整列表，请参阅[语言支持](language-support.md)。

若要了解有关标准、神经和自定义语音的详细信息，请参阅[文本到语音的概述](text-to-speech.md)。

## <a name="special-characters"></a>特殊字符

使用 SSML 时，请记住必须转义特殊字符，如引号、撇号和方括号。 有关详细信息，请参阅[可扩展标记语言（XML）1.0：附录 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支持的 SSML 元素

每个 SSML 文档都是通过 SSML 元素（或标记）创建的。 这些元素用于调整螺距、诗体论、成交量等。 以下各节详细说明了如何使用每个元素以及元素是必需的还是可选的。  

> [!IMPORTANT]
> 别忘了在属性值前后使用双引号。 格式正确的有效 XML 的标准要求将属性值括在双引号中。 例如，`<prosody volume="90">` 是格式正确的有效元素，但 `<prosody volume=90>` 不是。 SSML 无法识别不在引号中的属性值。

## <a name="create-an-ssml-document"></a>创建 SSML 文档

`speak` 是根元素，并且对于所有 SSML 文档都是**必需**的。 `speak` 元素包含重要信息，如版本、语言和标记词汇定义。

**语法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `version` | 指示用于解释文档标记的 SSML 规范的版本。 当前版本为1.0。 | 必选 |
| `xml:lang` | 指定根文档的语言。 该值可以包含小写字母、两个字母的语言代码（例如 `en`）或语言代码和大写字母（如 `en-US`）。 | 必选 |
| `xmlns` | 指定文档的 URI，该文档定义 SSML 文档的标记词汇（元素类型和属性名称）。 当前 URI 为 https://www.w3.org/2001/10/synthesis。 | 必选 |

## <a name="choose-a-voice-for-text-to-speech"></a>选择语音进行文本到语音转换

`voice` 元素是必需的。 它用于指定用于文本到语音转换的语音。

**语法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `name` | 标识用于文本到语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必选 |

**示例**

> [!NOTE]
> 此示例使用 `en-US-Jessa24kRUS` 语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多个语音

在 `speak` 元素中，可以为文本到语音输出指定多个声音。 这些语音可以采用不同的语言。 对于每个声音，文本必须在 `voice` 元素中进行包装。 

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `name` | 标识用于文本到语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必选 |

> [!IMPORTANT]
> 多个声音与单词边界功能不兼容。 需要禁用 word 边界功能才能使用多个声音。

### <a name="disable-word-boundary"></a>禁用 word 边界

根据语音 SDK 语言，将 `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` 属性设置为 `SpeechConfig` 对象的实例上 `false`。

# <a name="c"></a>[C#](#tab/csharp)

有关详细信息，请<a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`SetProperty` </a>。

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

有关详细信息，请<a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`SetProperty` </a>。

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

有关详细信息，请<a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`setProperty` </a>。

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

有关详细信息，请<a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`set_property_by_name` </a>。

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

有关详细信息，请<a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`setProperty` </a>。

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

有关详细信息，请<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`setPropertyTo` </a>。

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

有关详细信息，请<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">参阅<span class="docon docon-navigate-external x-hidden-focus"> </span>`setPropertyTo` </a>。

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>调整讲话风格

> [!IMPORTANT]
> 对讲话风格的调整只能与神经语音一起使用。

默认情况下，对于标准和神经声音，文本到语音服务合成文本使用非特定语言。 利用神经声音，你可以调整说话样式，使其与 `<mstts:express-as>` 元素表达 cheerfulness、理解或情绪。 这是语音服务独有的可选元素。

目前，以下神经声音支持讲话风格调整：
* `en-US-JessaNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`

更改将应用于句子级别，样式因语音而异。 如果样式不受支持，则该服务将以默认的非特定语言方式返回语音。

**语法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `type` | 指定讲话样式。 目前，说出的样式是特定于语音的。 | 如果调整用于 "神经" 的说话风格，则需要。 如果使用 `mstts:express-as`，则必须提供类型。 如果提供了无效的值，则将忽略此元素。 |

使用此表来确定每个神经语音支持的说话风格。

| 语音 | 类型 | 说明 |
|-------|------|-------------|
| `en-US-JessaNeural` | `type="cheerful"` | 表达非常好的情感 |
| | `type="empathy"` | 表达关心和理解 |
| | `type="chat"` | 用一种偶然、宽松的音调说话 |
| | `type="newscast"` | 表达与新闻广播类似的正式音调 |
| | `type="customerservice"` | 以友好且患者的方式与客户服务交流 |
| `pt-BR-FranciscaNeural` | `type="cheerful"` | 表达非常好的情感 |
| `zh-CN-XiaoxiaoNeural` | `type="newscast"` | 表达与新闻广播类似的正式音调 |
| | `type="sentiment"` | 传达触摸消息或故事 |

**示例**

此 SSML 代码段演示如何使用 `<mstts:express-as>` 元素将讲话样式更改为 `cheerful`。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>添加或删除中断/暂停

使用 `break` 元素可在单词之间插入暂停（或中断），或防止文本到语音服务自动添加暂停。

> [!NOTE]
> 如果此词或短语的合成语音非自然，请使用此元素替代单词或短语的文本到语音转换（TTS）的默认行为。 将 `strength` 设置为 "`none`" 以防止韵律中断，由文本到语音服务自动插入。

**语法**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `strength` | 使用以下值之一指定暂停的相对持续时间：<ul><li>none</li><li>x 弱</li><li>薄弱</li><li>中（默认值）</li><li>强</li><li>x-强</li></ul> | 可选 |
| `time` | 指定暂停的绝对持续时间（以秒或毫秒为单位）。 有效值的示例 `2s` 和 `500` | 可选 |

| 程度 | 说明 |
|----------|-------------|
| 无; 如果未提供任何值，则为 | 0 毫秒 |
| x 弱 | 250 ms |
| 薄弱 | 500 毫秒 |
| 中 | 750 毫秒 |
| 强 | 1000 ms |
| x-强 | 1250 ms |


**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和 `s` 元素分别用于表示段落和句子。 如果没有这些元素，则文本到语音服务会自动确定 SSML 文档的结构。

`p` 元素可以包含文本和以下元素： `audio`、`break`、`phoneme`、`prosody`、`say-as`、`sub`、`mstts:express-as`和 `s`。

`s` 元素可以包含文本和以下元素： `audio`、`break`、`phoneme`、`prosody`、`say-as`、`mstts:express-as`和 `sub`。

**语法**

```XML
<p></p>
<s></s>
```

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改善发音

`ph` 元素用于在 SSML 文档中使用拼音。 `ph` 元素只能包含文本，不能包含其他元素。 始终提供可读的语音作为回退。

拼音字母由电话组成，这些电话由字母、数字或字符组成，有时也会组合在一起。 每个电话都说明了语音的独特声音。 这与拉丁字母不同，其中任何字母都可能表示多个口述声音。 请考虑单词 "糖果" 和 "停止" 中字母 "c" 的不同发音，或在单词 "事物" 和 "这两个词" 中的字母组合 "th" 的不同发音。

**语法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `alphabet` | 指定综合属性 `ph` 中字符串的发音时要使用的拼音字母表。 指定字母表的字符串必须以小写字母指定。 下面是可以指定的可能字母表。<ul><li>`ipa` &ndash; 国际注音字母表</li><li>`sapi` &ndash; 语音服务注音</li><li>`ups` &ndash; 通用手机集</li></ul><br>字母表仅适用于元素中的 `phoneme`。 有关详细信息，请参阅[拼音字母参考](https://en.wikipedia.org/wiki/International_Phonetic_Alphabet)。 | 可选 |
| `ph` | 一个包含手机的字符串，该字符串指定 `phoneme` 元素中单词的发音。 如果指定的字符串包含无法识别的手机，则文本语音转换（TTS）服务将拒绝整个 SSML 文档并不生成文档中指定的任何语音输出。 | 如果使用音素，则是必需的。 |

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>使用自定义词典改善发音

TTS 有时不能准确地发音某个词，例如，公司或外部名称。 开发人员可以使用 `phoneme` 和 `sub` 标记在 SSML 中定义这些实体的读取，或使用 `lexicon` 标记来引用自定义词典文件来定义多个实体的读取。

**语法**

```XML
<lexicon uri="string"/>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `uri` | 外部 PLS 文档的地址。 | 必需。 |

**使用情况**

步骤1：定义自定义词典 

可以按自定义字典项的列表（存储为 .xml 或 pls 文件）定义实体的读取。

**示例**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

每个 `lexeme` 元素都是一个词典项。 `grapheme` 包含描述 `lexeme`的 orthograph 的文本。 读出窗体可作为 `alias`提供。 可以在 `phoneme` 元素中提供手机字符串。

`lexicon` 元素包含至少一个 `lexeme` 元素。 每个 `lexeme` 元素都包含至少一个 `grapheme` 元素以及一个或多个 `grapheme`、`alais`和 `phoneme` 元素。 `grapheme` 元素包含描述<a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"> </span></a>的文本。 `alias` 元素用于指示首字母缩写词或缩写词的发音。 `phoneme` 元素提供了描述 `lexeme` 的发音方式的文本。

有关自定义词典文件的详细信息，请参阅 W3C 网站上的[发音词典规范（PLS）版本 1.0](https://www.w3.org/TR/pronunciation-lexicon/) 。

步骤2：上传在步骤1中创建的自定义词典文件，你可以将其存储在任何位置，并且建议将其存储在 Microsoft Azure 中，例如[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

步骤3：在 SSML 中引用自定义词典文件

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"顺便" 将以 "方式" 读取。 将用提供的 IPA "bɛˈ ni ː nji" 读取 "Benigni"。  

**限制**
- 文件大小：自定义词典文件大小最大限制为100KB，如果超过此大小，合成请求将会失败。
- 词典缓存刷新：首次加载时，自定义词典将用 URI 作为其上的密钥来缓存该服务。 不会在15分钟内重新加载具有相同 URI 的字典，因此自定义词典更改需要等待15分钟才能生效。

**语音服务拼音设置**

在上面的示例中，我们使用的是国际拼音字母，也称为 IPA 手机集。 我们建议开发人员使用 IPA，因为它是国际标准。 考虑到 IPA 不容易记住，语音服务将为七种语言（`en-US`、`fr-FR`、`de-DE`、`es-ES`、`ja-JP`、`zh-CN`和 `zh-TW`）定义拼音集。

您可以使用 "`sapi` 作为 `alphabet` 属性的 refreshtype 作为自定义词典，如下所示：

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias> By the way </alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

有关详细的语音服务拼音字母的详细信息，请参阅[语音服务拼音设置](speech-ssml-phonetic-sets.md)。

## <a name="adjust-prosody"></a>调整诗体论

`prosody` 元素用于指定文本到语音输出的音调、countour、范围、速率、持续时间和音量的更改。 `prosody` 元素可以包含文本和以下元素： `audio`、`break`、`p`、`phoneme`、`prosody`、`say-as`、`sub`和 `s`。

由于韵律属性值在范围上可能会变化，因此语音识别器会将指定的值解释为所选语音的实际韵律值应该是什么。 文本到语音服务会限制或替换不受支持的值。 不受支持的值的示例为 1 MHz 或120卷的跨度。

**语法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `pitch` | 指示文本的基线间距。 你可以表达以下内容：<ul><li>一个绝对值，以数字开头，后跟 "Hz" （赫兹）。 例如，600 Hz。</li><li>一个相对值，表示为以 "+" 或 "-" 开头、"Hz" 或 "st" 开头的数字，用于指定要更改的间距。 例如： + 80 Hz 或-2st。 "St" 表示变更单位为半音，这是标准 diatonic 比例的半色调（半步）。</li><li>常量值：<ul><li>x-低</li><li>low</li><li>中</li><li>high</li><li>x-高</li><li>default</li></ul></li></ul>。 | 可选 |
| `contour` | 神经语音不支持等高线。 等高线表示螺距的变化。 这些更改表示为语音输出中指定时间位置的目标数组。 每个目标由参数对的集合定义。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每组参数中的第一个值指定间距更改的位置（以文本持续时间的百分比表示）。 第二个值指定升高或降低跨度的量，使用相对值或音调的枚举值（请参阅 `pitch`）。 | 可选 |
| `range` | 一个值，该值表示文本的间距范围。 您可以使用相同的绝对值、相对值或用于描述 `pitch`的枚举值表示 `range`。 | 可选 |
| `rate` | 指示文本的说话速率。 您可以将 `rate` 表达为：<ul><li>一个相对值，表示为作为默认值的倍数的数字。 例如，如果值为*1* ，则费率不会更改。 值*0.5*将导致一半。 如果值为*3* ，则会 tripling 速率。</li><li>常量值：<ul><li>x-慢</li><li>slow</li><li>中</li><li>快速</li><li>x-fast</li><li>default</li></ul></li></ul> | 可选 |
| `duration` | 语音合成（TTS）服务读取文本时应经过的时间段（以秒或毫秒为单位）。 例如 *，2* /2 或*1800ms*。 | 可选 |
| `volume` | 指示语音的音量级别。 你可以将卷表示为：<ul><li>一个绝对值，表示为0.0 到100.0 范围内的一个数字，从*quietest*到*loudest*。 例如，75。 默认值为100.0。</li><li>一个相对值，表示为以 "+" 或 "-" 开头的数字，用于指定更改卷的量。 例如，+ 10 或-5.5。</li><li>常量值：<ul><li>静音</li><li>x-soft</li><li>音</li><li>中</li><li>音量</li><li>大</li><li>default</li></ul></li></ul> | 可选 |

### <a name="change-speaking-rate"></a>更改语速

可以在单词或句子级应用到标准语音。 而说速度只能应用于句子级别的神经语音。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>更改音量

卷更改可以应用于 word 或句子级别的标准语音。 卷更改只能应用于句子级别的神经声音。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>更改音高

可以在单词或句子级将间距变化应用到标准语音。 而只能对句子级别的神经语音应用螺距更改。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>更改音高升降曲线

> [!IMPORTANT]
> 神经语音不支持音调等高线更改。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>假设元素

`say-as` 是一个可选元素，它指示元素文本的内容类型（如数字或日期）。 这为语音合成引擎提供有关如何发音文本的指导。

**语法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `interpret-as` | 指示元素的文本的内容类型。 有关类型的列表，请参阅下表。 | 必选 |
| `format` | 为可能具有不明确格式的内容类型提供有关元素文本的精确格式设置的其他信息。 SSML 为使用它们的内容类型定义格式（请参阅下表）。 | 可选 |
| `detail` | 指示要口述的详细信息的级别。 例如，此属性可能会请求语音合成引擎发音标点标记。 没有为 `detail`定义标准值。 | 可选 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

下面是 `interpret-as` 和 `format` 属性支持的内容类型。 仅当 `interpret-as` 设置为日期和时间时，才包括 `format` 特性。

| 解释为 | format | 解释 |
|--------------|--------|----------------|
| `address` | | 该文本称为地址。 语音合成引擎 pronounces：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />作为 "我在150th 的法庭 redmond 华盛顿州"。 |
| `cardinal`、`number` | | 此文本被称为基数数字。 语音合成引擎 pronounces：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />如 "有三个替代方法。" |
| `characters`、`spell-out` | | 此文本被称为单个字母（拼写出）。 语音合成引擎 pronounces：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />作为 "T E S T"。 |
| `date` | dmy、mdy、ymd、ydm、ym、my、md、dm、d、m、y | 文本被称为日期。 `format` 属性指定日期的格式（*d = day、m = month 和 y = year*）。 语音合成引擎 pronounces：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"今天是10月第19个 2016"。 |
| `digits`、`number_digit` | | 文本被称为单个数字的序列。 语音合成引擎 pronounces：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />为 "1 2 3 4 5 6 7 8 9"。 |
| `fraction` | | 该文本称为小数。 语音合成引擎 pronounces：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />为 "八分之三英寸"。 |
| `ordinal` | | 此文本被称为序号。 语音合成引擎 pronounces：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"选择第三个选项"。 |
| `telephone` | | 此文本被称为电话号码。 `format` 属性可以包含表示国家/地区代码的数字。 例如，"1" 表示美国，"39" 表示意大利。 语音合成引擎可能会使用此信息来指导其电话号码的发音。 电话号码也可能包含国家/地区代码，如果是，则优先于 `format`中的国家/地区代码。 语音合成引擎 pronounces：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"我的数字是区域代码 8 8 8 5 5 5 1 2 1 2"。 |
| `time` | hms12, hms24 | 该文本称为 "一次"。 `format` 属性指定时间是使用12小时制（hms12）还是24小时制（hms24）来指定。 使用冒号分隔表示小时、分钟和秒的数字。 下面是有效的时间示例：12:35、1:14:32、08:15 和02:50:45。 语音合成引擎 pronounces：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"训练离开为四个 A M"。 |

**使用情况**

`say-as` 元素只能包含文本。

**示例**

语音合成引擎的示例如下所示： "第一次请求的时间是10月第19个 20 10 上的一个房间，早到 12 35 下午。"
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>添加录制的音频

`audio` 是一个可选元素，可用于在 SSML 文档中插入 MP3 音频。 如果音频文件不可用或播放，则音频元素的正文可能包含明文或 SSML 标记。 此外，`audio` 元素可以包含文本和以下元素： `audio`、`break`、`p`、`s`、`phoneme`、`prosody`、`say-as`和 `sub`。

SSML 文档中包含的任何音频都必须满足以下要求：

* MP3 必须托管在可通过 Internet 访问的 HTTPS 终结点上。 HTTPS 是必需的，托管 MP3 文件的域必须提供有效的、受信任的 SSL 证书。
* MP3 必须是有效的 MP3 文件（MPEG v2）。
* 比特率必须为 48 kbps。
* 采样率必须为 16000 Hz。
* 单个响应中所有文本和音频文件的总时间不能超过90（90）秒。
* MP3 不得包含任何客户特定信息或其他敏感信息。

**语法**

```xml
<audio src="string"/></audio>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `src` | 指定音频文件的位置/URL。 | 如果在 SSML 文档中使用音频元素，则是必需的。 |

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>添加背景音频

通过 `mstts:backgroundaudio` 元素，您可以将背景音频添加到 SSML 文档中（或将音频文件与文本到语音混合一起使用）。 使用 `mstts:backgroundaudio` 您可以在后台循环播放音频文件，在文本到语音转换的开头处淡入，并在文本到语音的结尾处淡出。

如果提供的背景音频小于文本到语音转换或淡化，则会循环。 如果它的长度大于文本到语音转换的长度，它将在淡化完成后停止。

每个 SSML 文档只允许一个背景音频文件。 但是，您可以点播 `voice` 元素中的 `audio` 标记，将其他音频添加到 SSML 文档中。

**语法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**属性**

| Attribute | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `src` | 指定后台音频文件的位置/URL。 | 如果在 SSML 文档中使用背景音频，则是必需的。 |
| `volume` | 指定后台音频文件的卷。 **接受的值**： `0` `100` 包含的值。 默认值是 `1`。 | 可选 |
| `fadein` | 指定背景音频 "淡入" 的持续时间（以毫秒为单位）。 默认值为 `0`，这等同于不淡化。 **接受的值**： `0` `10000` 包含的值。  | 可选 |
| `fadeout` | 指定背景音频的持续时间（以毫秒为单位）。 默认值为 `0`，这等效于不淡出。**接受的值**： `0` `10000` 包含的值。  | 可选 |

**示例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>后续步骤

* [语言支持：语音、区域设置、语言](language-support.md)
