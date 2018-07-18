---
title: 语音训练听录指南 | Microsoft Docs
description: 了解如何准备文本以自定义语音服务的声学和语言模型以及语音字体。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "35366966"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>使用语音服务的听录指南

若要自定义“语音转文本”或“文本转语音”，则必须提供文本和语音。 文本中的每一行对应一个陈述。 文本称为一个脚本，必须按特定格式进行创建。

语音服务可执行一些规范化操作以使文本保持一致。 其他规范化任务必须在提交文本以进行训练前执行。 

本文介绍两种类型的规范化操作。 不同语言的指南略有不同。

## <a name="us-english-en-us"></a>美国英语 (en-US)

应仅使用 ASCII 字符集以纯文本格式写入上传至此服务的文本数据。 文件的每一行应包含单个陈述的文本。

务必避免使用扩展 (Latin-1) 或 Unicode 标点字符。 在文字处理程序中准备数据或从网页报废数据时，可能会无意中包括这些字符。 将这些字符替换为相应的 ASCII 替代字符。 例如：

| 要避免的字符 | 替换字符 |
|----- | ----- |
| “Hello world”（左右双引号） | "Hello world"（双引号） |
| John’s day（右单引号） | John's day（撇号） |
| it was good—no, it was great! （长破折号） | it was good--no, it was great! （连字符） |

### <a name="text-normalization-performed-by-the-service"></a>由服务执行的文本规范化

语音服务对文本脚本执行以下文本规范化操作。

*   所有文本全部小写
*   移除除单词内撇号外的所有标点
*   将数字扩展为口语形式，包括美元金额

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | i'm double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-you-must-perform"></a>必须手动执行的文本规范化

对文本脚本应用以下规范化。

*   应将缩写词写成字词来反映口语形式
*   非标准数值字符串（例如某些日期或账目格式）应写成字词
*   应按照发音听录包含非字母字符或字母数字字符的字词
*   保留与原字词发音相同的缩写词。 例如，radar、laser、RAM、NATO 和 Mr。
*   输入按单个字母发音的缩写词时，用空格分隔字母。 例如，IBM、CPU、FBI、TBD、NaN。 

下面是一些示例：

| 原始文本 | 规范化后 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr.Strangelove | Doctor Strangelove |
| James Bond 007 | James Bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>中文 (zh-CN)

上传到自定义语音服务的文本数据应使用带字节顺序标记的 UTF-8 编码。 文件的每一行应包含单个陈述的文本。

务必避免使用半角标点字符。 在文字处理程序中准备数据或从网页报废数据时，可能会无意中包括这些字符。 将它们替换为相应的全角字符。 例如：

| 要避免的字符 | 替换字符 |
|----- | ----- |
| "你好"（左右双引号） | “你好”（双引号） |
| 需要什么帮助? （问号） | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-service"></a>由服务执行的文本规范化

语音服务对文本脚本执行以下文本规范化操作。

*   删除所有标点
*   将数字扩展为口语形式
*   将全角字母转换为半角字母
*   所有英语字母大写

下面是一些示例。

| 原始文本 | 规范化后 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>必须手动执行的文本规范化

在导入文本前对其应用以下规范化。

*   应将缩写词写成字词来反映口语形式
*   此服务不包括所有数字数量。 用口语形式写数字字符串更可靠。

下面是一些示例。

| 原始文本 | 规范化后 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>其他语言

上传到“语音转文本”服务的文本数据必须使用带字节顺序标记的 UTF-8 编码。 文件的每一行应包含单个陈述的文本。

> [!NOTE]
> 以下示例使用德语。 但是，这些指南适用于非美国英语或中文的所有语言。

### <a name="text-normalization-performed-by-the-service"></a>由服务执行的文本规范化

语音服务对文本脚本执行以下文本规范化操作。

*   所有文本全部小写
*   删除所有标点，包括多种引号（可以保留 "test"、'test'、"test„ 或 «test»）
*   删除集中包含任意以下特殊字符的行：^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   将数字扩展为字词形式，包括美元或欧元金额
*   仅接受 a、o、u 的元音变音符；其余将替换为 th 或被丢弃

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |

### <a name="text-normalization-you-must-perform"></a>必须手动执行的文本规范化

在导入文本前对其应用以下规范化。

*   小数点应是“,”，而不是“.”：应是 2,3%，而不是 2.3%
*   小时和分钟之间的时间分隔符应是“:”，而不是“.”：如 12:00 Uhr
*   不替换“ca.”、“bzw.” 等缩写词。 我们建议使用完整形式。
*   删除四个主要的数学运算符：+、-、\*、/。 我们建议将其替换为文字形式：plus、minus、mal、geteilt。
*   比较运算符（=、<、>）采用相同方法：gleich、kleiner als、grösser als
*   分数（例如 3/4）采用文字形式（例如“drei viertel”，而不是 ¾）
*   将 € 符号替换为文字形式“Euro”

下面是一些示例。

| 原始文本 | 用户规范化后 | 系统规范化后
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [在 C# 中识别语音](quickstart-csharp-windows.md)
