---
title: 语音合成标记语言 (SSML)-语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音合成标记语言来控制文本到语音转换中的发音和韵律。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604821"
---
# <a name="speech-synthesis-markup-language-ssml"></a>语音合成标记语言 (SSML)

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，可让开发人员指定如何输入的文本转换为合成语音使用文本到语音转换服务。 与纯文本相比，SSML 允许开发人员优化的语调、 发音，说到率、 卷和多个文本到语音转换输出。 自动处理正常标点，例如暂停一段时间后或用问号的句子结束时使用正确的声调。

语音服务实现的 SSML 基于 World Wide Web Consortium[语音合成标记语言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、 日语和朝鲜语字符计为两个字符来进行计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>Standard、 神经，和自定义语音

从标准和神经声音，选择或创建你自己的自定义语音唯一的产品或品牌。 75 标准语音可在多个 45 语言和区域设置，以及 5 神经语音有 4 个语言和区域设置。 有关支持的语言、区域设置和语音（神经和标准）的完整列表，请参阅[语言支持](language-support.md)。

若要了解有关神经，标准和自定义语音的详细信息，请参阅[文本到语音转换概述](text-to-speech.md)。

## <a name="supported-ssml-elements"></a>受支持的 SSML 元素

每个 SSML 文档会使用 SSML 元素 （或标记） 创建。 这些元素用于调整间距、 诗体论、 卷和的详细信息。 以下部分详细介绍如何使用每个元素，以及当元素是必需还是可选。  

> [!IMPORTANT]
> 别忘了使用双引号括住属性值。 格式正确的有效 XML 标准要求属性值括在双引号。 例如，`<prosody volume="90">`是格式正确的有效元素，但`<prosody volume=90>`不是。 SSML 可能无法识别不可用引号引起来的属性值。

## <a name="create-an-ssml-document"></a>创建一个 SSML 文档

`speak` 是根元素，且**需**所有 SSML 文档。 `speak`元素包含重要信息，如版本、 语言和标记词汇定义。

**语法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 版本 | 指示用于解释文档标记的 SSML 规范的版本。 当前版本为 1.0。 | 需要 |
| Xml: lang | 指定根文档的语言。 值可能包含一个小写字符、 两个字母语言代码 (例如， **en**)，或语言代码和大写的国家/地区 (例如， **EN-US**)。 | 需要 |
| xmlns | 指定文档，用于定义 SSML 文档的标记词汇 （元素类型和属性名称） 的 URI。 在当前的 URI 是 https://www.w3.org/2001/10/synthesis 。 | 需要 |

## <a name="choose-a-voice-for-text-to-speech"></a>选择文本到语音转换的声音

`voice`元素是必需的。 它用于指定用于文本到语音的语音。

**语法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本到语音转换输出的语音。 有关受支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 需要 |

**示例**

> [!NOTE]
> 此示例使用`en-US-Jessa24kRUS`语音。 有关受支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多个语音

在`speak`元素中，您可以指定多个语音文本到语音转换输出。 这些语音可以是不同的语言。 每个语音，文本必须包装在`voice`元素。

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本到语音转换输出的语音。 有关受支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 需要 |

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>调整讲话风格

> [!IMPORTANT]
> 此功能只适用于神经语音。

默认情况下，文本到语音转换服务将合成的标准和神经语音使用非特定的讲话风格的文本。 使用神经语音，您可以调整来表达 cheerfulness、 全情投入、 或与情绪的说话风格`<mstts:express-as>`元素。 这是唯一的 Azure 语音服务的可选元素。

目前，这些神经语音支持说话样式调整：
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

在句子级别中，应用更改和样式因语音。 如果样式不受支持，该服务将返回语音在默认的非特定讲话风格。

**语法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| type | 指定的说话风格。 目前，讲话风格是特定语音。 | 如果调整神经语音的说话风格，需要。 如果使用`mstts:express-as`，则必须提供类型。 如果提供一个无效值，则将忽略此元素。 |

使用此表来确定每个神经语音支持的讲话风格。

| 语音 | Type | 描述 |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | 表示为正数，并且高兴情感 |
| | type=`empathy` | 表示某种意义上的关注并了解 |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 表示正式音，类似于新闻 |
| | type=`sentiment` | 传达触摸消息或一篇文章 |

**示例**

此 SSML 代码段说明了如何`<mstts:express-as>`元素用于更改到的说话风格`cheerful`。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>添加或删除中断/暂停

使用`break`元素之间的单词，插入暂停 （或中断） 或阻止自动添加文本到语音转换服务暂停。

> [!NOTE]
> 使用此元素的单词或短语的替代文本到语音转换 (TTS) 的默认行为，如果该单词或短语的合成的语音听起来不自然。 设置`strength`到`none`以防止自动插入的文本到语音服务韵律中断。

**语法**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 强度 | 指定相对持续时间的暂停使用以下值之一：<ul><li>无</li><li>x 弱</li><li>弱</li><li>中 （默认值）</li><li>强</li><li>x 强</li></ul> | 可选 |
| time | 指定以秒或毫秒暂停的绝对持续时间。 有效的值的示例包括 2 秒和 500 | 可选 |

| 强度 | 描述 |
|----------|-------------|
| 无，或如果没有提供值 | 0 毫秒 |
| x 弱 | 250 毫秒 |
| 弱 | 500 毫秒 |
| 中 | 750 毫秒 |
| 强 | 1000 毫秒 |
| x 强 | 1250 ms |


**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定的段落和句子

`p` 和`s`元素用于分别表示段落和句子。 如果没有这些元素，文本到语音转换服务自动确定 SSML 文档的结构。

`p`元素可能包含文本和以下元素： `audio`， `break`， `phoneme`， `prosody`， `say-as`， `sub`， `mstts:express-as`，和`s`。

`s`元素可能包含文本和以下元素： `audio`， `break`， `phoneme`， `prosody`， `say-as`， `mstts:express-as`，和`sub`。

**语法**

```XML
<p></p>
<s></s>
```

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改进发音

`ph`元素用于在 SSML 文档中的语音发音。 `ph`元素只能包含文本，没有其他元素。 始终作为回退提供用户可读的语音。

手机，字母、 数字或字符，有时结合组成由构成的注音字母表。 每个电话介绍语音的独特声音。 这与拉丁字母表，其中的任何字母可能表示多个语音听起来。 请考虑字母"c"中的单词"糖果"和"停止"，不同发音或字母组合不同发音"th"单词"thing"和"与"中。

**语法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 字母 | 指定语音字母表合成中字符串的发音时要使用`ph`属性。 指定字母表的字符串必须以小写字母指定。 以下是可以指定可能字母表。<ul><li>ipa&ndash;国际音标</li><li>sapi&ndash;语音 API 电话设置</li><li>ups&ndash;通用电话设置</li></ul>字母表仅应用于的元素中音素。 有关详细信息，请参阅[语音字母表引用](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 可选 |
| p h | 一个包含指定中单词的发音的手机字符串`phoneme`元素。 如果指定的字符串包含无法识别的手机，文本到语音转换 (TTS) 服务将拒绝整个 SSML 文档，并生成无指定文档中的语音输出。 | 如果使用音素，必需。 |

**示例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>调整诗体论

`prosody`元素用于指定对间距、 countour、 范围、 速率，持续时间和文本到语音转换输出的卷的更改。 `prosody`元素可能包含文本和以下元素： `audio`， `break`， `p`， `phoneme`， `prosody`， `say-as`， `sub`，和`s`。

因为韵律属性值可以变化范围广泛，语音识别器将分配的值解释为选中的语音的实际韵律值应该是什么的建议。 文本到语音转换服务限制，或将替换为不受支持的值。 不受支持的值的示例是 1 MHz 的间距或 120 的卷。

**语法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 间距 | 指示文本的基线间距。 可能表示作为间距：<ul><li>绝对值，表示为"Hz"（赫兹） 后跟一个数字。 例如，600 Hz。</li><li>一个相对值，表示为数字的前面有"+"或"-"并后接"Hz"或"st"，指定量更改间距。 例如: + 80 Hz 或 2st。 "St"指示更改单元是 semitone，这是标准 diatonic 刻度上音 （半步骤） 的一半。</li><li>常量的值：<ul><li>x-low</li><li>低</li><li>中</li><li>高</li><li>x-高</li><li>default</li></ul></li></ul>. | 可选 |
| 轮廓线 | 不支持神经语音的轮廓。 轮廓表示间距的语音内容更改为目标，请参阅语音输出中指定的时间位置的数组。 每个目标由参数对集的定义。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每组参数中的第一个值的文本的持续时间的百分比指定间距更改的位置。 第二个值指定要引发或降低间距，使用用于间距的相关值或枚举值的量 (请参阅`pitch`)。 | 可选 |
| range  | 一个表示间距的文本范围的值。 您可能表达`range`使用相同的绝对值、 相对值或枚举值用来描述`pitch`。 | 可选 |
| 速率  | 指示文本的语速。 您可能表示`rate`作为：<ul><li>一个相对值，表示为数字的作为乘数的默认值。 例如，值*1*不导致任何更改速率。 值为 *.5*导致费率的一半来。 值为*3*导致两倍，以便的速率。</li><li>常量的值：<ul><li>x-slow</li><li>slow</li><li>中</li><li>快速</li><li>x-fast</li><li>default</li></ul></li></ul> | 可选 |
| duration  | 语音时应经过的时间段合成 (TTS) 服务中读取文本，以秒或毫秒为单位。 例如， *2s*或*1800ms*。 | 可选 |
| 卷  | 指示讲话声音的音量级别。 您可能会表示为卷：<ul><li>绝对值，从表示为介于 0.0 到 100.0 的范围内的数字*quietest*到*高音*。 例如，75。 默认值为 100.0。</li><li>一个相对值，表示为数字的前面有"+"或"-"，它指定量更改的量。 例如，+ 10 或-5.5。</li><li>常量的值：<ul><li>无提示</li><li>x-soft</li><li>soft</li><li>中</li><li>音量高</li><li>x 高音</li><li>default</li></ul></li></ul> | 可选 |

### <a name="change-speaking-rate"></a>更改语速

说到速率可以应用于在 word 或句子级别的标准语音。 而谈到速率仅应用从句子层面神经语音。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>更改音量

卷更改可以应用于在 word 或句子级别的标准语音。 而只能从句子层面神经语音到应用批量更改。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>更改音高

间距更改可以应用于在 word 或句子级别的标准语音。 而只能从句子层面神经语音到应用间距更改。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>更改音高升降曲线

> [!IMPORTANT]
> 间距 contour 更改不支持与神经语音。

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>后续步骤

* [语言支持：语音、区域设置、语言](language-support.md)
