---
title: 快速入门：创建自定义语音助手 - 语音服务
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "80241760"
---
在本快速入门中，你将使用[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) 创建连接到你已创建并配置的机器人的自定义语音助手应用程序。 如果需要创建机器人，请参阅[相关教程](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md)，了解更全面的指南。

满足一些先决条件后，连接自定义语音助手只需几个步骤：
> [!div class="checklist"]
> * 通过订阅密钥和区域创建 `BotFrameworkConfig` 对象。
> * 使用以上的 `BotFrameworkConfig` 对象创建 `DialogServiceConnector` 对象。
> * 使用 `DialogServiceConnector` 对象，开始单个言语的侦听过程。
> * 检查返回的 `ActivityReceivedEventArgs`。
