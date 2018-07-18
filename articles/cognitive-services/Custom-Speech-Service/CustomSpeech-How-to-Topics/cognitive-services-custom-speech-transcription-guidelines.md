---
title: Azure 上的自定义语音服务中的听录准则 | Microsoft Docs
description: 了解如何为认知服务中的自定义语音服务准备数据。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 2785a35ac7583ac3d9503cb721d10078d86aa365
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365544"
---
# <a name="transcription-guidelines"></a>听录准则
为确保最大限度地使用文本数据进行声学和语言模型自定义，应遵循下列听录准则。 这些准则是特定于语言的。

## <a name="text-normalization"></a>文本规范化

为了实现声学或语言模型自定义的最佳使用，必须对文本数据进行规范化处理，即转换为系统可读的标准且明确的形式。 本部分介绍了导入数据时自定义语音服务执行的文本规范化以及用户必须在数据导入前执行的文本规范化。

## <a name="inverse-text-normalization"></a>反向文本规范化

将“原始”的未格式化文本转换回格式化文本的过程（即区分大小写并带有标点）被称为反向文本规范化 (ITN)。 根据 Microsoft 认知服务语音 API 返回的结果执行 ITN。 通过使用自定义语音服务部署的自定义终结点使用与 Microsoft 认知服务语音 API 相同的 ITN。 但是，此服务当前不支持自定义 ITN，因此将不会在识别结果中格式化自定义语言模型引入的新术语。

## <a name="transcription-guidelines-for-en-us"></a>英语 (en-US) 听录准则

应仅使用 ASCII 可打印字符集以纯文本格式写入上传至此服务的文本数据。 文件的每一行应仅包含单个语句的文本。

避免使用 Unicode 标点字符十分重要。 如果在文字处理程序中准备数据或从网页报废数据时，这种情况可能会在无意中发生。 将这些字符替换为相应的 ASCII 替代字符。 例如：

| 要避免的 Unicode | ASCII 替换字符 |
|----- | ----- |
| “Hello world”（打开和关闭双引号） | "Hello world"（双引号） |
| John’s day（右单引号） | John's day（撇号） |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>自定义语音服务执行的文本规范化

此服务将根据导入为语言数据集的数据或声学数据集的听录执行以下文本规范化。 这包括：

*   所有文本全部小写
*   删除除单词内撇号外的所有标点
*   将数字扩展为口头形式，包括美元金额

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | i’m double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>用户需要文本规范化

为确保最大限度地使用数据，应在导入数据前将下列规范化规则应用到数据。

*   应将缩写写成单词来反映口语形式
*   非标准数值字符串应写成单词
*   应按照发音听录带非字母字符或字母数字字符的单词
*   常见的首字母缩略词可以保留为字母间不含句号或空格的单个实体，但所有其他首字母缩略词应写成单独的字母，每个字母用单个空格分开

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr.Strangelove | Doctor Strangelove |
| James Bond 007 | james bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>中文 (zh-CN) 听录准则

上传至自定义语音服务的文本数据应使用“UTF-8 编码（包括 BOM）”。 文件的每一行应仅包含单个语句的文本。

避免使用半角标点字符十分重要。 如果在文字处理程序中准备数据或从网页报废数据时，这种情况可能会在无意中发生。 将这些字符替换为相应的全角字符。 例如：

| 要避免的 Unicode | ASCII 替换字符 |
|----- | ----- |
| “你好”（左右双引号） | "你好"（双引号） |
| 需要什么帮助? （问号） | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>自定义语音服务执行的文本规范化

此语音服务将根据导入为语言数据集的数据或声学数据集的听录执行以下文本规范化。 这包括：

*   删除所有标点
*   将数字扩展为口语形式
*   将全角字母扩展为半角字母。
*   所有文本全部大写

下面是一些示例：

| 原始文本 | 规范化后 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>用户需要文本规范化

为确保最大限度地使用数据，应在导入数据_前_将下列规范化规则应用到数据。

*   应将缩写写成单词来反映口语形式
*   此服务不包括所有数字数量。 以口语形式写出数值字符串更为可靠

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>德语 (de-DE) 听录准则

上传至自定义语音服务的文本数据应仅使用“UTF-8 编码（包括 BOM）”。 文件的每一行应仅包含单个语句的文本。

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>自定义语音服务执行的文本规范化

此服务将根据导入为语言数据集的数据或声学数据集的听录执行以下文本规范化。 这包括：

*   所有文本全部小写
*   删除所有标点，包括英语或德语引号（"test"、'test'、“test„ 或 «test» 都可以）
*   丢弃包含任意特殊字符的任意行，其中包括：^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   将数字扩展为口头形式，包括美元或欧元金额
*   我们仅接受 a、o、u 这种元音变音符；其他元音变音符则被“th”替换或被丢弃

下面是一些示例

| 原始文本 | 规范化后 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |


### <a name="text-normalization-required-by-users"></a>用户需要文本规范化

为确保最大限度地使用数据，应在导入数据前将下列规范化规则应用到数据。

*   小数点应当是 , 而不是 . 例如，应当是 2,3% 而不是 2.3%
*   小时和分钟之间的时间分隔符应当是 : 而不是 ,，例如 12:00 Uhr
*   不替换“ca.”、“bzw.” 等缩写。 我们建议使用完整形式以获得正确的发音。
*   删除五个主要的数学运算符：+、-、\*、/。
 我们建议使用其文字形式的加、减、乘、除进行替换。
*   同样适用于比较运算符：(=, <, >) - 等于、小于、大于
*   以单词形式‘drei viertel’使用分数 3/4 代替 ¾
*   将 € 符号替换为单词形式的“Euro”


下面是一些示例

| 原始文本 | After user's normalization | After system normalization
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>后续步骤
* [如何使用自定义语音转文本终结点](cognitive-services-custom-speech-create-endpoint.md)
* 通过你的[自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)提高准确性
* 通过一个[自定义声学模型](cognitive-services-custom-speech-create-language-model.md)提高准确性
