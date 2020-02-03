---
title: 创建自定义关键字-语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终侦听关键字（或短语）。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717026"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>使用 Speech Studio 创建自定义关键字

设备始终侦听关键字（或短语）。 例如，"你好 Cortana" 是 Cortana 助手的关键字。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。

本文介绍如何为设备创建自定义关键字。

## <a name="create-your-keyword"></a>创建关键字

在可以使用自定义关键字之前，需要使用[Speech Studio](https://aka.ms/sdsdk-speechportal)上的 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页创建关键字。 提供关键字后，它会生成一个部署到设备上的文件。

1. 转到[Speech Studio](https://aka.ms/sdsdk-speechportal)并**登录**，如果还没有语音订阅，请选择 "[**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页上，创建一个**新项目**。 

1. 输入**名称**和可选**描述**，并选择语言。 每种语言都需要一个项目，并且支持目前仅限于 en-us 语言。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择你的项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要启动新的关键字模型，请单击 "**训练模型**"。

1. 为关键字模型输入**名称**和可选**说明**，并键入所选**关键字**，并单击 "**下一步**"。 我们有一些[指导原则](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)来帮助选择有效的关键字。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 门户现在将为关键字创建候选发音。 通过单击 "播放" 按钮来倾听每个候选项，并删除任何不正确的发音旁边的检查。 仅选中好发音后，单击 "**训练**" 开始生成关键字。 

    ![查看关键字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型最多可能需要30分钟时间。 模型完成后，关键字列表将从**处理**更改为**成功**。 然后，你可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 将 .zip 文件保存到计算机。 需要此文件将自定义关键字部署到设备。

## <a name="next-steps"></a>后续步骤

通过[语音设备 SDK 快速入门](https://aka.ms/sdsdk-quickstart)测试自定义关键字。
