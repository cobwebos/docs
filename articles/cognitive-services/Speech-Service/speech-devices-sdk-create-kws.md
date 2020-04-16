---
title: 创建自定义关键字 - 语音服务
titleSuffix: Azure Cognitive Services
description: 您的设备始终在侦听关键字（或短语）。 当用户说出关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分您的设备并增强品牌的有效方法。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400062"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>使用语音工作室创建自定义关键字

您的设备始终在侦听关键字（或短语）。 例如，"嘿，Cortana"是 Cortana 助手的关键字。 当用户说出关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分您的设备并增强品牌的有效方法。

在本文中，您将了解如何为设备创建自定义关键字。

## <a name="create-your-keyword"></a>创建关键字

在使用自定义关键字之前，您需要使用[语音工作室](https://aka.ms/sdsdk-speechportal)上的[自定义关键字](https://aka.ms/sdsdk-wakewordportal)页面创建关键字。 提供关键字后，它会生成部署到设备的文件。

1. 转到[语音工作室](https://aka.ms/sdsdk-speechportal)并**登录**，或者，如果您尚未订阅语音订阅，请选择"[**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在[自定义关键字](https://aka.ms/sdsdk-wakewordportal)页面，创建新**项目**。 

1. 输入**名称**，可选**说明**，然后选择语言。 您需要每种语言一个项目，并且支持当前仅限于 en-US 语言。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择您的项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 要启动一个新的关键字模型，请单击 **"训练"模型**。

1. 输入关键字**模型的名称**，并在您选择的**关键字**中输入可选**的"描述"，** 然后单击"**下一步**"。 我们有一些[准则](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)，可以帮助你选择有效的关键字。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 门户现在将为您的关键字创建候选发音。 通过单击播放按钮收听每个考生，并删除任何发音不正确的检查。 只检查好发音后，单击 **"训练"** 开始生成关键字。 

    ![查看关键字](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型可能需要长达三十分钟。 当模型完成时，关键字列表将从 **"处理**"更改为 **"成功**"。 然后，您可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 将 .zip 文件保存到计算机。 您需要此文件才能将自定义关键字部署到您的设备。

## <a name="next-steps"></a>后续步骤

使用[语音设备 SDK 快速入门](https://aka.ms/sdsdk-quickstart)测试自定义关键字。
