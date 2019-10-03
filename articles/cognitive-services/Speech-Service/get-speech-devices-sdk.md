---
title: 获取语音设备 SDK
titleSuffix: Azure Cognitive Services
description: “语音服务”适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 在本文中, 你将了解如何访问语音设备 SDK 并开始开发。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b60c8735833c3012523c5cc0d7eea6a50e9d10b6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559693"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>获取认知服务语音设备 SDK

语音设备 SDK 是一种 pretuned 库, 旨在与专门构建的开发工具包和各种麦克风阵列配置配合使用。

## <a name="choose-a-development-kit"></a>选择开发工具包

|设备|规格|描述|方案|
|--|--|--|--|
|[Roobo 智能音频开发工具包](https://ddk.roobo.com)</br>[安装](speech-devices-sdk-roobo-v1.md) / [快速](speech-devices-sdk-android-quickstart.md)入门Roobo![智能音频开发工具包](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array、ARM SOC、WIFI、音频输出、IO。 </br>[Android](speech-devices-sdk-android-quickstart.md)|用于适应 Microsoft Mic Array 和 front 处理 SDK 的第一种语音设备 SDK, 用于开发高质量的脚本和语音方案|对话脚本, 智能发言人, 语音代理, 可穿戴|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)</br>[设置](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [快速](speech-devices-sdk-windows-quickstart.md)入门Azure![Kinect 深色](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic Array RGB 和 Depth 相机。 </br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|具有高级人工智能 (AI) 传感器的开发人员工具包, 用于构建复杂的计算机视觉和语音模型。 它将同类最佳的空间麦克风阵列和深度相机与视频相机和方向传感器组合在一起, 这一切都在具有多种模式、选项和 Sdk 的一小设备中, 可容纳各种计算类型。|对话脚本, 自动机器人, 智能生成|
|Roobo 智能音频开发工具包2![Roobo 智能音频开发工具包2](media/speech-devices-sdk/device-roobo-v2.jpg)|7麦克风阵列, ARM SOC, WIFI, 蓝牙, IO。 </br>Linux|第2代语音设备 SDK, 它在经济高效的参考设计中提供备选 OS 和更多功能。|对话脚本, 智能发言人, 语音代理, 可穿戴|
|URbetter T11 开发板![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7麦克风阵列, ARM SOC, WIFI, 以太网, HDMI, USB 摄像机。 </br>Linux|一种可适应 Microsoft Mic 阵列并支持扩展 i/o (如 HDMI/以太网和更多 USB 外围设备) 的行业级语音设备 SDK|对话会议、教育、医院、机器人、OTT Box、语音代理、驱动器到|

## <a name="download-the-speech-devices-sdk"></a>下载语音设备 SDK

下载[语音设备 SDK](https://aka.ms/sdsdk-download)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音设备 SDK 入门](https://aka.ms/sdsdk-quickstart)
