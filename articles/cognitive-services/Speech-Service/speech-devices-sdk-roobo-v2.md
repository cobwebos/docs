---
title: 语音设备 SDK Roobo 智能音频开发套件 v2 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音设备 SDK、Roobo 智能音频开发工具包 v2 入门的先决条件和说明。
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371579"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>设备： Roobo 智能音频开发套件 v2

本文为 Roobo 智能音频开发工具包2提供了特定于设备的信息。

## <a name="set-up-the-development-kit"></a>设置开发工具包

1. 开发工具包拥有两个 micro USB 连接器。 左侧的连接器用于为开发工具包接通电源，在下图中突出显示为 Power。 右侧的连接器用于控制开发工具包，在图中标记为 Debug。 
    ![连接开发套件](media/speech-devices-sdk/roobo-v2-connections.png)
1. 使用 micro USB 数据线为开发工具包接通电源，将电源端口连接到 PC 或电源适配器。 顶部板下的绿色电源指示灯将亮起。
1. 若要控制开发工具包，请使用另一条 micro USB 电缆将调试端口连接到计算机。 使用高质量的电缆以确保可靠的通信至关重要。
1. 以圆形方式定向您的开发套件 - 直立，麦克风面向天花板，如上图所示


## <a name="development-information"></a>开发信息

有关详细信息，请参阅[Roobo 开发指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio-recordplay"></a>音频录制/播放

DDK2 音频操作可通过以下方式执行：
* 使用 ALSA 开源库及其应用程序。
* 使用 appmainprog 接口执行应用程序开发。 DDK2音频相关软件框架使用标准ALSA框架，您可以使用libasound. 所以直接开发软件。 因此，您可以使用 ALSA 的录制和播放直接录制和播放音频。
