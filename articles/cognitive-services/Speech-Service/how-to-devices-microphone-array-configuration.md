---
title: 如何配置麦克风阵列语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何配置麦克风阵列，以便语音设备 SDK 可以使用它。
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82781753"
---
# <a name="how-to-configure-a-microphone-array"></a>如何配置麦克风阵列

本文介绍如何配置[麦克风阵列](https://aka.ms/sdsdk-microphone)。 它包括设置工作角度，以及如何选择用于语音设备 SDK 的麦克风。

语音设备 SDK 最适用于根据[我们的准则](https://aka.ms/sdsdk-microphone)设计的麦克风阵列。 麦克风阵列配置可以由操作系统提供，也可以通过以下方法之一提供。

通过从一组固定的配置中进行选择，语音设备 SDK 最初支持麦克风阵列。

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

在 Windows 上，麦克风阵列配置由音频驱动程序提供。

从1.11.0 开始，语音设备 SDK 还支持[JSON 文件](https://aka.ms/sdsdk-micarray-json)中的配置。


## <a name="windows"></a>Windows
在 Windows 上，会自动从音频驱动程序获取麦克风阵列几何信息。 因此，属性`DeviceGeometry`、 `SelectedGeometry`和`MicArrayGeometryConfigFile`是可选的。 我们使用提供`MicArrayGeometryConfigFile`的[JSON 文件](https://aka.ms/sdsdk-micarray-json)仅获取 beamforming 范围。

如果使用`AudioConfig::FromMicrophoneInput`指定麦克风阵列，则使用指定的麦克风。 如果未指定或`AudioConfig::FromDefaultMicrophoneInput`调用麦克风，则使用默认麦克风，该麦克风是在 Windows 的 "声音设置" 中指定的。
语音设备 SDK 中的 Microsoft 音频堆栈仅支持对 16 KHz 整数倍数的采样速率进行下采样。

## <a name="linux"></a>Linux
在 Linux 上，必须提供麦克风几何信息。 使用`DeviceGeometry`和`SelectedGeometry`仍受支持。 还可以使用`MicArrayGeometryConfigFile`属性通过 JSON 文件提供此文件。 与 Windows 类似，beamforming 范围可由 JSON 文件提供。

如果使用`AudioConfig::FromMicrophoneInput`指定麦克风阵列，则使用指定的麦克风。 如果未指定或`AudioConfig::FromDefaultMicrophoneInput`调用麦克风，则会从名为*default*的 ALSA 设备进行录制。 默认情况下，*默认值*始终指向卡0设备0，但用户可以在`asound.conf`文件中对其进行更改。 

语音设备 SDK 中的 Microsoft 音频堆栈仅支持作为 16 KHz 整数倍数的采样率的缩减率。 此外，还支持以下格式：32位 IEEE little endian float、32位 little endian 有符号整数、24位 little endian 有符号整数、16位 little endian 有符号整数和8位有符号整数。

## <a name="android"></a>Android
语音设备 SDK 目前仅支持[Roobo v1](speech-devices-sdk-android-quickstart.md) 。 行为与以前的版本相同，不同之`MicArrayGeometryConfigFile`处在于 now 属性可用于指定包含 beamforming 范围的 JSON 文件。

## <a name="microphone-array-configuration-json"></a>麦克风阵列配置 JSON

用于麦克风阵列几何配置的 JSON 文件将遵循[json 架构](https://aka.ms/sdsdk-micarray-json)。 下面是遵循该架构的一些示例。


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


或


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

或

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择语音设备](get-speech-devices-sdk.md)
