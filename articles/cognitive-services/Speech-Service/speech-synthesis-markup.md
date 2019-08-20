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
ms.openlocfilehash: 12d556fd9c37b83a919b830d155250e9eaa64128
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624247"
---
# <a name="speech-synthesis-markup-language-ssml"></a>语音合成标记语言 (SSML)

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言, 它允许开发人员使用文本到语音服务来指定如何将输入文本转换为合成语音。 与纯文本相比, SSML 允许开发人员精细调整文本到语音输出的音调、发音、语速、音量和更多内容。 正常标点, 如在一个句点后暂停, 或使用正确的 intonation, 当以问号结束的句子将自动处理。

SSML 的语音服务实现基于万维网联合会的[语音合成标记语言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、日语和朝鲜语字符作为两个字符进行计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>标准、神经和自定义语音

选择 "标准" 和 "神经" 语音, 或创建自己的产品或品牌独有的自定义声音。 75多种语言和区域设置中提供了 75 45 多项标准语音, 5 种神经声音提供4种语言和区域设置。 有关支持的语言、区域设置和语音（神经和标准）的完整列表，请参阅[语言支持](language-support.md)。

若要了解有关标准、神经和自定义语音的详细信息, 请参阅[文本到语音的概述](text-to-speech.md)。

## <a name="special-characters"></a>特殊字符

使用 SSML 转换文本到合成的语音时, 请记住, 与 XML 一样, 必须对特殊字符 (如引号、撇号和方括号) 进行转义。 有关详细信息, 请[参阅可扩展标记语言 (XML) 1.0:附录 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支持的 SSML 元素

每个 SSML 文档都是通过 SSML 元素 (或标记) 创建的。 这些元素用于调整螺距、诗体论、成交量等。 以下各节详细说明了如何使用每个元素以及元素是必需的还是可选的。  

> [!IMPORTANT]
> 别忘了在属性值前后使用双引号。 格式正确的有效 XML 的标准要求将属性值括在双引号中。 例如, `<prosody volume="90">`是一个格式正确的有效元素, 但`<prosody volume=90>`不是。 SSML 无法识别不在引号中的属性值。

## <a name="create-an-ssml-document"></a>创建 SSML 文档

`speak`是根元素, 并且是所有 SSML 文档**所必需**的。 `speak`元素包含重要信息, 如版本、语言和标记词汇定义。

**语法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| version | 指示用于解释文档标记的 SSML 规范的版本。 当前版本为1.0。 | 必填 |
| xml: lang | 指定根文档的语言。 该值可以包含小写字母、两个字母的语言代码 (例如, **en**) 或语言代码和大写字母 (例如**en-us**)。 | 必填 |
| xmlns | 指定文档的 URI, 该文档定义 SSML 文档的标记词汇 (元素类型和属性名称)。 当前 URI 是 https://www.w3.org/2001/10/synthesis 。 | 必填 |

## <a name="choose-a-voice-for-text-to-speech"></a>选择语音进行文本到语音转换

元素`voice`是必需的。 它用于指定用于文本到语音转换的语音。

**语法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本到语音输出的语音。 有关支持的语音的完整列表, 请参阅[语言支持](language-support.md#text-to-speech)。 | 必填 |

**示例**

> [!NOTE]
> 此示例使用`en-US-Jessa24kRUS`语音。 有关支持的语音的完整列表, 请参阅[语言支持](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多个语音

`speak`在元素中, 可以为文本到语音输出指定多个声音。 这些语音可以采用不同的语言。 对于每个声音, 都必须将文本包装在`voice`元素中。

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| name | 标识用于文本到语音输出的语音。 有关支持的语音的完整列表, 请参阅[语言支持](language-support.md#text-to-speech)。 | 必填 |

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
> 此功能只能与神经语音一起使用。

默认情况下, 对于标准和神经声音, 文本到语音服务合成文本使用非特定语言。 利用神经声音, 你可以调整说话样式, 使其与`<mstts:express-as>`元素一起表达 cheerfulness、理解或情绪。 这是 Azure 语音服务独有的可选元素。

目前, 以下神经声音支持讲话风格调整:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

更改将应用于句子级别, 样式因语音而异。 如果样式不受支持, 则该服务将以默认的非特定语言方式返回语音。

**语法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| type | 指定讲话样式。 目前, 说出的样式是特定于语音的。 | 如果调整用于 "神经" 的说话风格, 则需要。 如果使用`mstts:express-as`, 则必须提供类型。 如果提供了无效的值, 则将忽略此元素。 |

使用此表来确定每个神经语音支持的说话风格。

| 语音 | type | 描述 |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | 表达非常好的情感 |
| | type=`empathy` | 表达关心和理解 |
| | type=`chat` | 用一种偶然、宽松的音调说话 |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 表达与新闻广播类似的正式音调 |
| | type=`sentiment` | 传达触摸消息或故事 |

**示例**

此 SSML 代码段演示如何`<mstts:express-as>`使用元素将讲话样式更改为。 `cheerful`

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

使用元素`break`可在单词之间插入暂停 (或中断), 或防止文本到语音服务自动添加暂停。

> [!NOTE]
> 如果此词或短语的合成语音非自然, 请使用此元素替代单词或短语的文本到语音转换 (TTS) 的默认行为。 设置`strength` 为`none`以防止韵律中断, 该中断由文本到语音服务自动插入。

**语法**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 程度 | 使用以下值之一指定暂停的相对持续时间:<ul><li>无</li><li>x 弱</li><li>弱</li><li>中 (默认值)</li><li>强</li><li>x-强</li></ul> | 可选 |
| 时间 | 指定暂停的绝对持续时间 (以秒或毫秒为单位)。 有效值的示例有2和500 | 可选 |

| 程度 | 描述 |
|----------|-------------|
| 无; 如果未提供任何值, 则为 | 0毫秒 |
| x 弱 | 250 ms |
| 弱 | 500 毫秒 |
| 中等 | 750 毫秒 |
| 强 | 1000 ms |
| x-强 | 1250 ms |


**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p`和`s`元素分别用于表示段落和句子。 如果没有这些元素, 则文本到语音服务会自动确定 SSML 文档的结构。

`prosody` `break` `phoneme` `audio`元素可能`sub`包含文本和以下元素:、、、、 `say-as` 、、`s`和。 `mstts:express-as` `p`

`phoneme` `break` `audio` `prosody`元素可能`say-as`包含文本和以下元素:`mstts:express-as`、、、、、和`sub`。 `s`

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

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改善发音

`ph`元素用于在 SSML 文档中使用拼音。 `ph`元素只能包含文本, 不能包含其他元素。 始终提供可读的语音作为回退。

拼音字母由电话组成, 这些电话由字母、数字或字符组成, 有时也会组合在一起。 每个电话都说明了语音的独特声音。 这与拉丁字母不同, 其中任何字母都可能表示多个口述声音。 请考虑单词 "糖果" 和 "停止" 中字母 "c" 的不同发音, 或在单词 "事物" 和 "这两个词" 中的字母组合 "th" 的不同发音。

**语法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 逆 | 指定综合`ph`属性中字符串的发音时要使用的拼音字母表。 指定字母表的字符串必须以小写字母指定。 下面是可以指定的可能字母表。<ul><li>ipa &ndash;国际注音字母表</li><li>sapi &ndash;语音 API 电话集</li><li>ups &ndash;通用电话集</li></ul>字母表仅适用于元素中的音素。 有关详细信息, 请参阅[拼音字母参考](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 可选 |
| 三相 | 一个字符串, 该字符串包含指定`phoneme`元素中单词的发音的手机。 如果指定的字符串包含无法识别的手机, 则文本语音转换 (TTS) 服务将拒绝整个 SSML 文档并不生成文档中指定的任何语音输出。 | 如果使用音素, 则是必需的。 |

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

`prosody`元素用于指定文本到语音输出的音调、countour、范围、速率、持续时间和音量的更改。 `phoneme` `break` `p` `audio`元素可能`say-as`包含文本和以下元素:、、、、 `prosody` 、、`s`和。 `sub` `prosody`

由于韵律属性值在范围上可能会变化, 因此语音识别器会将指定的值解释为所选语音的实际韵律值应该是什么。 文本到语音服务会限制或替换不受支持的值。 不受支持的值的示例为 1 MHz 或120卷的跨度。

**语法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| 推广 | 指示文本的基线间距。 你可以表达以下内容:<ul><li>一个绝对值, 以数字开头, 后跟 "Hz" (赫兹)。 例如, 600Hz。</li><li>一个相对值, 表示为以 "+" 或 "-" 开头、"Hz" 或 "st" 开头的数字, 用于指定要更改的间距。 例如: + 80Hz 或-2st。 "St" 表示变更单位为半音, 这是标准 diatonic 比例的半色调 (半步)。</li><li>常量值:<ul><li>x-低</li><li>低</li><li>中等</li><li>高</li><li>x-高</li><li>默认</li></ul></li></ul>. | 可选 |
| 轮廓 | 神经语音不支持等高线。 等高线表示语音内容在语音输出中指定时间位置作为目标数组的间距变化。 每个目标由参数对的集合定义。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每组参数中的第一个值指定间距更改的位置 (以文本持续时间的百分比表示)。 第二个值指定升高或降低跨度的量, 使用相对值或音调的枚举值 (请参见`pitch`)。 | 可选 |
| range  | 一个值, 该值表示文本的间距范围。 您可以使用`range`相同的绝对值、相对值或用于描述`pitch`的枚举值表示。 | 可选 |
| 分级  | 指示文本的说话速率。 可以表达`rate`如下:<ul><li>一个相对值, 表示为作为默认值的倍数的数字。 例如, 如果值为*1* , 则费率不会更改。 如果值为*0.5* , 则会产生一半。 如果值为*3* , 则会 tripling 速率。</li><li>常量值:<ul><li>x-慢</li><li>slow</li><li>中等</li><li>fast</li><li>x-fast</li><li>默认</li></ul></li></ul> | 可选 |
| 持续时间  | 语音合成 (TTS) 服务读取文本时应经过的时间段 (以秒或毫秒为单位)。 例如, 2 /2 或*1800ms*。 | 可选 |
| 卷  | 指示语音的音量级别。 你可以将卷表示为:<ul><li>一个绝对值, 表示为0.0 到100.0 范围内的一个数字, 从*quietest*到*loudest*。 例如, 75。 默认值为100.0。</li><li>一个相对值, 表示为以 "+" 或 "-" 开头的数字, 用于指定更改卷的量。 例如 + 10 或-5.5。</li><li>常量值:<ul><li>自行</li><li>x-soft</li><li>音</li><li>中等</li><li>音量</li><li>大</li><li>默认</li></ul></li></ul> | 可选 |

### <a name="change-speaking-rate"></a>更改语速

可以在单词或句子级应用到标准语音。 而说速度只能应用于句子级别的神经语音。

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

卷更改可以应用于 word 或句子级别的标准语音。 卷更改只能应用于句子级别的神经声音。

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

可以在单词或句子级将间距变化应用到标准语音。 而只能对句子级别的神经语音应用螺距更改。

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
> 神经语音不支持音调等高线更改。

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

## <a name="add-recorded-audio"></a>添加录制的音频

`audio`是一个可选元素, 可用于在 SSML 文档中插入 MP3 音频。 如果音频文件不可用或播放, 则音频元素的正文可能包含明文或 SSML 标记。 `audio`此外, 元素还可以包含文本和以下元素: `phoneme` `break` `s` `p` `audio` 、、、`say-as`、 `sub`、、和。 `prosody`

SSML 文档中包含的任何音频都必须满足以下要求:

* MP3 必须托管在可通过 Internet 访问的 HTTPS 终结点上。 HTTPS 是必需的, 托管 MP3 文件的域必须提供有效的、受信任的 SSL 证书。
* MP3 必须是有效的 MP3 文件 (MPEG v2)。
* 比特率必须为 48 kbps。
* 采样率必须为 16000 Hz。
* 单个响应中所有文本和音频文件的总时间不能超过 90 (90) 秒。
* MP3 不得包含任何客户特定信息或其他敏感信息。

**语法**

```xml
<audio src="string"/></audio>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| src | 指定音频文件的位置/URL。 | 如果在 SSML 文档中使用音频元素, 则是必需的。 |

**示例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>添加背景音频

`mstts:backgroundaudio`元素允许向 SSML 文档添加背景音频 (或将音频文件与文本到语音混合一起使用)。 利用`mstts:backgroundaudio` , 你可以在后台循环播放音频文件, 在文本到语音转换开始时淡入, 并在文本到语音的结尾处淡出。

如果提供的背景音频小于文本到语音转换或淡化, 则会循环。 如果它的长度大于文本到语音转换的长度, 它将在淡化完成后停止。

每个 SSML 文档只允许一个背景音频文件。 但是, 你可以在`audio` `voice`元素内点播标记, 以便将其他音频添加到 SSML 文档中。

**语法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**属性**

| 特性 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| src | 指定后台音频文件的位置/URL。 | 如果在 SSML 文档中使用背景音频, 则是必需的。 |
| 卷 | 指定后台音频文件的卷。 **接受值**: `0`到`100`包含的值。 默认值为 `1`。 | 可选 |
| fadein | 指定背景音频淡入的持续时间 (以毫秒为单位)。 默认值为`0`, 它等效于不淡化。 **接受值**: `0`到`10000`包含的值。  | 可选 |
| fadeout | 指定背景音频的持续时间 (以毫秒为单位)。 默认值为`0`, 它等效于不淡出。**接受值**: `0`到`10000`包含的值。  | 可选 |

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
