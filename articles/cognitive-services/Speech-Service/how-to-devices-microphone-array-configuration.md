---
title: 如何配置麦克风阵列 - 语音服务
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82781753"
---
# <a name="how-to-configure-a-microphone-array"></a>如何配置麦克风阵列

本文介绍如何配置[麦克风阵列](https://aka.ms/sdsdk-microphone)。 它包括设置工作角度，以及如何选择用于语音设备 SDK 的麦克风。

语音设备 SDK 最适合与根据[我们的指南](https://aka.ms/sdsdk-microphone)设计的麦克风阵列配合使用。 麦克风阵列配置可以由操作系统提供，也可以通过以下方法之一提供。

语音设备 SDK 最初通过选择一组固定配置中的配置来支持麦克风阵列。

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

在 Windows 上，麦克风阵列配置由音频驱动程序提供。

从 v1.11.0 开始，语音设备 SDK 还支持 [JSON 文件](https://aka.ms/sdsdk-micarray-json)中的配置。


## <a name="windows"></a>Windows
在 Windows 上，会自动从音频驱动程序获取麦克风阵列几何信息。 因此，属性 `DeviceGeometry`、`SelectedGeometry` 和 `MicArrayGeometryConfigFile` 是可选的。 只将通过使用 `MicArrayGeometryConfigFile` 提供的 [JSON 文件](https://aka.ms/sdsdk-micarray-json)用于获取波束赋形范围。

如果使用 `AudioConfig::FromMicrophoneInput` 指定了麦克风阵列，则使用指定的麦克风。 如果未指定麦克风或调用了 `AudioConfig::FromDefaultMicrophoneInput`，则使用在 Windows 的“声音”设置中指定的默认麦克风。
语音设备 SDK 中的 Microsoft 音频堆栈仅支持 16 KHz 整数倍的频率样本的向下采样。

## <a name="linux"></a>Linux
在 Linux 上，必须提供麦克风的几何信息。 仍然支持使用 `DeviceGeometry` 和 `SelectedGeometry`。 还可以使用 `MicArrayGeometryConfigFile` 属性通过 JSON 文件提供。 与 Windows 类似，波束赋形范围可由 JSON 文件提供。

如果使用 `AudioConfig::FromMicrophoneInput` 指定了麦克风阵列，则使用指定的麦克风。 如果未指定麦克风或调用了 `AudioConfig::FromDefaultMicrophoneInput`，则会从名为 default 的 ALSA 设备进行记录。 默认情况下，“default”始终指向 card 0 device 0，但用户可以在 `asound.conf` 文件中对其进行更改。 

语音设备 SDK 中的 Microsoft 音频堆栈仅支持以 16 KHz 整数倍数的采样率进行下采样。 此外，还支持以下格式：32 位 IEEE little endian 浮点数、32 位 little endian 有符号整数、24 位 little endian 有符号整数、16 位 little endian 有符号整数和 8 位有符号整数。

## <a name="android"></a>Android
目前，语音设备 SDK 仅支持 [Roobo v1](speech-devices-sdk-android-quickstart.md)。 行为与以前的版本相同，但 `MicArrayGeometryConfigFile` 属性现可用于指定包含波束赋形范围的 JSON 文件。

## <a name="microphone-array-configuration-json"></a>麦克风阵列配置 JSON

用于麦克风阵列几何配置的 JSON 文件将遵循 [JSON 架构](https://aka.ms/sdsdk-micarray-json)。 下面是遵循该架构的一些示例。


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
