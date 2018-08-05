---
title: Azure 认知服务语音服务
description: 了解如何使用语音服务认知服务自定义发音。
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: c7c06fc2f33baa7357fd5f945414daf2bc6e4858
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284932"
---
# <a name="enable-custom-pronunciation"></a>启用自定义发音
借助自定义发音，用户可以定义语音形式和字词或术语显示。 它适用于处理自定义术语，如产品名称或首字母缩略词。 只需使用发音文件（简单的 .txt 文件）即可。

工作原理如下。 在一个 .txt 文件中，可以输入多个自定义发音条目。 结构如下：

```
Display form <Tab> Spoken form <Newline>
```

*示例*：

| 显示形式 | 口头形式 |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>口头形式要求
口头形式必须为小写，可以在导入过程中强制实施。 此外，还必须在数据导入工具中执行检查。 禁止在口头形式或显示形式中使用制表位。 不过，禁止在显示形式中使用的字符可能更多（例如，~ 和 ^）。

每个 .txt 文件都可以包含多个条目。 有关示例，请参阅下面的屏幕截图：

![包含多个首字母缩略词发音条目的记事本屏幕截图](media/stt/custom-speech-pronunciation-file.png)

口头形式是显示形式的语音序列。 它由字母、字词或音节组成。 暂无其他任何有助于规划口头形式的指南或一系列标准。 

## <a name="supported-pronunciation-characters"></a>支持的发音字符
目前，只有英语 (en-US) 和德语 (de-de) 支持自定义发音。 下表列出了可用于（在自定义发音文件中）表示术语的口头形式的字符集： 

| 语言 | 字符 |
|---------- |----------|
| 英语 (en-US) | a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |
| 德语 (de-de) | ä、ö、ü、ẞ、a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |

> [!NOTE]
> （发音文件中的）术语显示形式应与语言顺应数据集中的编写方式相同。

## <a name="requirements-for-the-display-form"></a>显示形式要求
显示形式只能是自定义字词、术语、首字母缩略词或将现有字词组合到一起的复合词。 还可以输入常见字词的可选发音。 

>[!NOTE]
>不建议使用此功能来重新规划常见字词或修改口头形式。 最好是运行解码器，以确定是否有一些不常见词汇（如缩写、技术词汇和外来词）未正确解码。 若有，请将它们添加到自定义发音文件。 在语言模型中，应务必仅使用字词的显示形式。 

## <a name="requirements-for-the-file-size"></a>文件大小要求
包含发音条目的 .txt 文件不得超过 1MB。 通常情况下，无需通过此文件上传大量数据。 大多数自定义发音文件可能只有几 KB 大小。 对于所有区域设置，.txt 文件的编码都应为 UTF-8 BOM。 对于英语区域设置，ANSI 也是可接受的。

## <a name="next-steps"></a>后续步骤
* 通过创建[自定义声学模型](how-to-customize-acoustic-models.md)提高识别准确性
* 通过创建[自定义语言模型](how-to-customize-language-model.md)提高识别准确性
