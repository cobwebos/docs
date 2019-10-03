---
title: 创建自定义唤醒字词-语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终在侦听唤醒词（或短语）。 当用户说唤醒词时，设备会将所有后续音频发送到云，直到用户停止说话为止。 自定义唤醒词是区分设备和加强品牌效应的有效方式。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553113"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>使用语音服务创建自定义唤醒词

设备始终在侦听唤醒词（或短语）。 例如，“Hey Cortana”是 Cortana 助手的唤醒词。 当用户说唤醒词时，设备会将所有后续音频发送到云，直到用户停止说话为止。 自定义唤醒词是区分设备和加强品牌效应的有效方式。

本文介绍如何为设备创建自定义唤醒词。

## <a name="choose-an-effective-wake-word"></a>选择有效的唤醒词

选择唤醒词时请考虑以下准则：

* 唤醒词应该是英文单词或短语。 说该词应该不超过两秒钟。

* 4 到 7 个音节的单词效果最好。 例如，“Hey, Computer”是一个很好的唤醒词， 而只是“Hey”则是一个糟糕的唤醒词。

* 唤醒词应遵循常见的英语发音规则。

* 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，“computerama”可能是一个很好的唤醒词。

* 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。

* 避免使用可能有其他发音的唤醒词。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。

* 不要使用特殊字符、符号或数字。 例如，“Go#”和“20 + cats”不会是好的唤醒词。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择商标词作为唤醒词，请确保你拥有该商标，或者获得商标所有者的许可才能使用该词。 Microsoft 对你选择的唤醒词可能引起的任何法律问题不承担任何责任。

## <a name="create-your-wake-word"></a>创建唤醒词

在你的设备上使用自定义唤醒字词之前, 你需要使用 Microsoft 自定义唤醒字词生成服务创建一个唤醒词。 提供唤醒字词后, 服务将生成一个文件, 该文件将部署到开发工具包以在设备上启用唤醒词。

1. 转到[自定义语音服务门户](https://aka.ms/sdsdk-speechportal)并登录, 如果你没有语音订阅, 请选择 **"** [**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"

    ![自定义语音服务门户](media/speech-devices-sdk/wake-word-4.png)

1. 在所选的 "[自定义唤醒字词](https://aka.ms/sdsdk-wakewordportal)" 页上键入, 并单击 "**添加唤醒词**"。 我们有一些[指导原则](#choose-an-effective-wake-word)来帮助选择有效的关键字。 目前仅支持 en-us 语言。

    ![输入唤醒词](media/speech-devices-sdk/wake-word-5.png)

1. 将创建唤醒词的三个替代发音。 您可以选择喜欢的所有发音。 然后选择 "**提交**" 以生成唤醒字词。 如果要更改唤醒词, 请先删除现有的单词, 将鼠标悬停在发音行后, 将显示 "删除" 图标。

    ![查看唤醒字词](media/speech-devices-sdk/wake-word-6.png)

1. 生成模型最多可能需要一分钟。 系统将提示您下载该文件。

    ![下载唤醒字词](media/speech-devices-sdk/wake-word-7.png)

1. 将 .zip 文件保存到计算机。 需要此文件将自定义唤醒词部署到开发工具包。

## <a name="next-steps"></a>后续步骤

测试[语音设备 SDK 快速入门](https://aka.ms/sdsdk-quickstart)中的自定义唤醒词。
