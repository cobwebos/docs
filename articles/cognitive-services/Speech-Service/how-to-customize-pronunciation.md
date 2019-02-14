---
title: 自定义发音 - 语音服务
titlesuffix: Azure Cognitive Services
description: 了解如何使用语音服务自定义发音。 借助自定义发音，可以定义语音形式和字词或术语显示。 它适用于处理自定义术语，如产品名称或首字母缩略词。 只需使用发音文件（简单的 .txt 文件）即可。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0a3dfce10fc8ea76bc8f99e2459295bc637017dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878402"
---
# <a name="enable-custom-pronunciation"></a>启用自定义发音

借助自定义发音，你可以定义语音形式和字词或术语显示。 它适用于处理自定义术语，如产品名称或首字母缩略词。 只需使用发音文件（简单的 .txt 文件）即可。

工作原理如下。 在一个 .txt 文件中，可以输入多个自定义发音条目。 结构如下：

```
Display form <Tab> Spoken form <Newline>
```

下表显示了几个示例：

| 显示形式 | 口头形式 |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>口头形式要求
口头形式必须为小写，可以在导入过程中强制实施。 此外，还需要在数据导入工具中提供检查。 禁止在口头形式或显示形式中使用制表位。 不过，禁止在显示形式中使用的字符可能更多（例如，~ 和 ^）。

每个 .txt 文件都可以有多个条目，如下图所示：

![首字母缩略词发音的示例](media/stt/custom-speech-pronunciation-file.png)

口头形式是显示形式的语音序列。 它由字母、字词或音节组成。 目前，没有进一步的指南或标准集来帮助你规划口头形式。

## <a name="supported-pronunciation-characters"></a>支持的发音字符
目前，英语 (en-US) 和德语 (de-de) 支持自定义发音。 下表列出了可用于（在自定义发音文件中）表示术语的口头形式的字符集：

| 语言 | 字符 |
|---------- |----------|
| 英语 (en-US) | a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |
| 德语 (de-de) | ä、ö、ü、?、a、b、c、d、e、f、g、h、i、j、k、l、o、p、q、r、s、t、u、v、w、x、y、z |

> [!NOTE]
> （发音文件中的）术语显示形式应与语言顺应数据集中的编写方式相同。

## <a name="requirements-for-the-display-form"></a>显示形式要求
显示形式只能是自定义字词、术语、首字母缩略词或将现有字词组合到一起的复合词。 还可以输入常见字词的可选发音。

>[!NOTE]
>不建议使用此功能来重新规划常见字词或修改口头形式。 最好是运行解码器，以确定是否有一些不常见词汇（如缩写、技术词汇或外来词）未正确解码。 若有，请将它们添加到自定义发音文件。 在语言模型中，应始终仅使用字词的显示形式。

## <a name="requirements-for-the-file-size"></a>文件大小要求
包含发音条目的 .txt 文件的大小限制为 1 兆字节（对于免费层密钥，则为 1 KB）。 通常情况下，无需通过此文件上传大量数据。 大多数自定义发音文件可能只有几千字节 (KB) 大小。 对于所有区域设置，.txt 文件的编码都应为 UTF-8 BOM。 对于英语区域设置，ANSI 也是可接受的。

## <a name="next-steps"></a>后续步骤
* 通过创建[自定义声学模型](how-to-customize-acoustic-models.md)提高识别准确性。
* 通过创建[自定义语言模型](how-to-customize-language-model.md)提高识别准确性。
