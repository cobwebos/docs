---
title: 人为标记的转录准则-语音服务
titleSuffix: Azure Cognitive Services
description: 如果希望提高识别准确率，尤其是在删除或不正确地替换字词时所导致的问题，您需要将人为标记的转录与音频数据一起使用。 什么是人为标记的转录？ 这很简单，它们是音频文件的单词逐字、逐字的转录。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4f0cffbb65192a3698df0c658742147aea354b70
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744536"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>如何创建人机标记的转录

如果希望提高识别准确率，尤其是在删除或不正确地替换字词时所导致的问题，您需要将人为标记的转录与音频数据一起使用。 什么是人为标记的转录？ 这很简单，它们是音频文件的单词逐字、逐字的转录。

若要改善识别，建议使用大量的脚本数据，建议提供10到1000小时的脚本数据。 在此页上，我们将查看旨在帮助你创建高质量转录的准则。 本指南通过区域设置进行分解，其中包含美国英语、普通话中文和德语部分。

## <a name="us-english-en-us"></a>美国英语 (en-US)

必须以纯文本形式（仅使用 ASCII 字符）提供带标签的转录。 避免使用拉丁语-1 或 Unicode 标点符号字符。 在从字处理应用程序复制文本或从网页中抓取数据时，通常会无意中添加这些字符。 如果存在这些字符，请确保用适当的 ASCII 替换更新它们。

以下是几个示例：

| 要避免的字符 | 替换字符 | 说明 |
|---------------------|--------------|-------|
| "Hello world" | “Hello world” | 开始和结束引号已替换为适当的 ASCII 字符。 |
| John’s day | John's day | 撇号已替换为适当的 ASCII 字符。 |
| it was good—no, it was great! | it was good--no, it was great! | 用两个连字符替换长划线。 |

### <a name="text-normalization-for-us-english"></a>简体中文文本规范化

文本规范化是指在训练模型时使用的一致格式的单词转换。 某些规范化规则将自动应用于文本，但是，我们建议在准备人贴标签的脚本数据时使用这些准则：

* 写出单词中的缩写。
* 写出非标准数字字符串（如会计术语）。
* 非字母字符或混合字母数字字符应转录。
* 不应编辑单词（如 "雷达图"、"激光"、"RAM" 或 "个 NATO"）的缩写词。
* 写出以空格分隔的每个字母分隔的缩写。

下面是一些应在脚本上执行的规范化示例：

| 原始文本 | 规范化后的文本 |
|---------------|--------------------------|
| ScriptoBruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+ | My blood type is O positive |
| Water is H20 | Water is H 2 O |
| Play OU812 by Van Halen | Play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

以下规范化规则将自动应用于转录：

* 使用小写字母。
* 删除单词内除撇号之外的所有标点。
* 将数字扩展为单词/口述形式，例如美元金额。

下面是一些在脚本上自动执行的规范化示例：

| 原始文本 | 规范化后的文本 |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>普通话（zh-chs）

人标记的转录 for 普通话中文音频必须是使用字节顺序标记编码的 UTF-8。 避免使用半角标点字符。 当你在文字处理程序中准备数据或擦除网页中的数据时，可能会无意中包含这些字符。 如果存在这些字符，请确保用适当的全角替换更新它们。

以下是几个示例：

| 要避免的字符 | 替换字符 | 说明 |
|---------------------|--------------|-------|
| "你好" | "你好" | 开始和结束引号已替换为适当的字符。 |
| 需要什么帮助? | 需要什么帮助？ | 问号已替换为适当的字符。 |

### <a name="text-normalization-for-mandarin-chinese"></a>普通话中文的文本规范化

文本规范化是指在训练模型时使用的一致格式的单词转换。 某些规范化规则将自动应用于文本，但是，我们建议在准备人贴标签的脚本数据时使用这些准则：

* 写出单词中的缩写。
* 用口语形式写数字字符串。

下面是一些应在脚本上执行的规范化示例：

| 原始文本 | 规范化后的文本 |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

以下规范化规则将自动应用于转录：

* 删除所有标点
* 将数字扩展为口头形式
* 将全角字母转换为半角字母
* 对所有英语单词使用大写字母

下面是一些在脚本上自动执行的规范化示例：

| 原始文本 | 规范化后的文本 |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>德语（de）和其他语言

人为德语音频（以及其他非英语或普通话汉语）标记的转录必须是使用字节顺序标记编码的 UTF-8。 应该为每个音频文件提供一个人为标记的脚本。

### <a name="text-normalization-for-german"></a>德语文本规范化

文本规范化是指在训练模型时使用的一致格式的单词转换。 某些规范化规则将自动应用于文本，但是，我们建议在准备人贴标签的脚本数据时使用这些准则：

*   将小数点写入为 "，" 而不是 "."。
*   写入时间分隔符为 "："，而不是 "."（例如：12:00 Uhr）。
*   不替换“ca.” 等缩写。 建议使用完全口述的形式。
*   删除四个主要的数学运算符（+、-、\* 和 /）。 建议将它们替换为书写形式： "plus"、"减号" 和 "geteilt"。
*   删除比较运算符（=、< 和 >）。 我们建议其替换为“gleich”、“kleiner als”和“grösser als”。
*   以书面形式写入分数，例如3/4 （例如： "drei viertel"，而不是3/4）。
*   将 "€" 符号替换为其写入格式 "Euro"。

下面是一些应在脚本上执行的规范化示例：

| 原始文本 | 用户规范化后的文本 | 系统规范化后的文本 |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

以下规范化规则将自动应用于转录：

* 对所有文本使用小写字母。
* 删除所有标点，包括各种类型的引号（"test"、"test"、"test" 和« test »都是正常的）。
* 从此集中丢弃具有任何特殊字符的行：存储，¥¥¥¦§©，®°±
* 将数字扩展到口头形式，包括美元或欧元量。
* 仅对、o 和你接受元音。 其他将被 "th" 替换或被丢弃。

下面是一些在脚本上自动执行的规范化示例：

| 原始文本 | 规范化后的文本 |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>后续步骤

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
