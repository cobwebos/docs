---
title: 语音设备 SDK Roobo 智能音频开发工具包 v2-语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门的先决条件和说明 Roobo 智能音频开发工具包 v2。
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 5cf851bc9333004c0e14713cde44f470fb8c0c02
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304282"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>设备： Roobo 智能音频开发工具包 v2

本文提供 Roobo 智能音频开发人员 Kit2 的特定于设备的信息。

## <a name="set-up-the-development-kit"></a>设置开发工具包

1. 开发工具包拥有两个 micro USB 连接器。 左侧的连接器用于为开发工具包接通电源，在下图中突出显示为 Power。 右侧的连接器用于控制开发工具包，在图中标记为 Debug。 
    ![连接开发工具包](media/speech-devices-sdk/roobo-v2-connections.png)
1. 使用 micro USB 数据线为开发工具包接通电源，将电源端口连接到 PC 或电源适配器。 顶部板下的绿色电源指示灯将亮起。
1. 若要控制开发工具包，请使用第二个微 USB 电缆将调试端口连接到计算机。 必须使用高质量的电缆才能确保可靠的通信。
1. 以循环方式将开发套件定向到正面朝天花板的麦克风，如以上所示


## <a name="development-information"></a>开发信息

有关更多开发信息，请参阅[Roobo 开发指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio-recordplay"></a>音频录制/播放

可以通过以下方式执行 DDK2 音频操作：
* 使用 ALSA 开放源代码库及其应用程序。
* 使用 appmainprog 接口来执行应用程序开发。 DDK2 音频相关软件框架使用标准 ALSA framework，你可以使用 libasound。 因此直接开发软件。 因此，您可以使用 ALSA 的 arecord 和 aplay 直接记录和播放音频。
