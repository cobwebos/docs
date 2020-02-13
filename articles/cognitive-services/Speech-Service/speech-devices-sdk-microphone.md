---
title: 语音设备 SDK 麦克风阵列建议
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 麦克风阵列建议。 建议将这些数组几何图形用于 Microsoft 音频堆栈。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168139"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>语音设备 SDK 麦克风阵列建议

本文介绍如何为语音设备 SDK 设计麦克风数组。

语音设备 SDK 最适用于根据以下准则设计的麦克风阵列，其中包括麦克风几何和组件选择。 还提供了有关集成和电气注意事项的指南。

## <a name="microphone-geometry"></a>麦克风几何

建议将以下数组几何图形用于 Microsoft 音频堆栈。 对于特定的应用程序、用户方案和设备外形规格，具有更多麦克风的位置和环境噪音拒绝的位置会得到改善。

|     | 循环数组 |     | 线性数组 |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mic | 7 | 4 | 4 | 2 |
| Geometry | 6个外，1个中心，半径 = 42.5 毫米，平均间距 | 3个外，1个中心，半径 = 42.5 毫米，平均间距 | 长度 = 120 mm，间距 = 40 mm | 间距 = 40 mm |

应根据上述每个数组中所示的编号，按0增加麦克风频道的顺序。 Microsoft 音频堆栈需要额外的音频播放流来执行回声取消。

## <a name="component-selection"></a>组件选择

应选择麦克风组件以便准确地重现无噪音和失真的信号。

选择麦克风时建议的属性如下：

| 参数 | 建议 |
| --------- | ----------- |
| SNR | \>= 65 dB （1 kHz 信号 94 dBSPL，加权噪音） |
| 振幅匹配 | ± 1 dB @ 1 kHz |
| 阶段匹配 | ±2° @ 1 kHz |
| 声音重载点（AOP） | \>= 120 dBSPL （THD = 10%） |
| 比特率 | 最小24位 |
| 采样率 | 最小 16 kHz\* |
| 频率响应 | ± 3 dB，200-8000 Hz 浮动掩码\* |
| 可靠性 | 存储温度范围-40 ° C 到70° C<br />操作温度范围-20 ° C 到55° C |

_高质量通信（VoIP）应用程序可能需要 \*更高的采样率或 "更宽" 频率范围_

良好的组件选择必须与良好的 electroacoustic 集成配对，以避免从而降低所用组件的性能。 唯一用例也可能需要额外的需求（例如：工作温度范围）。

## <a name="microphone-array-integration"></a>麦克风阵列集成

麦克风阵列集成到设备时，其性能将不同于组件规范。 确保在集成后正确匹配麦克风非常重要。 因此，在任何固定增益或 EQ 之后测量的设备性能应满足以下建议：

| 参数          | 建议                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB （1 kHz 信号 94 dBSPL，加权噪音） |
| 输出敏感度 | -26 dBFS/Pa @ 1 kHz （推荐）                  |
| 振幅匹配 | ± 2 dB，200-8000 Hz                                |
| THD%\*             | ≤1%，200-8000 Hz，94 dBSPL，第5个顺序             |
| 频率响应 | ± 6 dB，200-8000 Hz 浮动掩码\*\*              |

\*\*_需要较低的扭曲音箱来度量 THD （例如 NEUMANN KH120）_

_高质量通信（VoIP）应用程序可能需要 \*\*"更宽" 频率范围_

## <a name="speaker-integration-recommendations"></a>演讲者集成建议

由于包含发言人的语音识别设备需要回声取消功能，因此提供了有关扬声器选择和集成的其他建议。

| 参数 | 建议 |
| --------- | ----------- |
| 线性注意事项 | 演讲者引用后无非线性处理，否则，需要基于硬件的环回引用流 |
| 扬声器环回 | 通过 WASAPI、私有 Api、自定义 ALSA 插件（Linux）提供，或通过固件通道提供 |
| THD% | 第三个 Octave 区段最少5个订单，70 dBA 播放 @ 0.8 m ≤6.3%，315-500 Hz ≤5%，630-5000 Hz |
| 回音耦合到麦克风 | \>-10 dB TCLw，使用 ITU-T G G a i l G G 方法，规范化为 mic level<br />TCLw = TCLwmeasured \+ （度量级别-目标输出敏感度）<br />TCLw = TCLwmeasured \+ （度量级别-（-26）） |

## <a name="integration-design-architecture"></a>集成设计体系结构

将麦克风集成到设备时，需要以下体系结构准则：

| 参数 | 建议 |
| --------- | -------------- |
| 麦克风端口相似性 | 阵列中的所有麦克风端口都具有相同的长度 |
| Mic 端口尺寸 | 端口大小Ø 0.8-1.0 毫米。 端口长度/端口直径 \< 2 |
| Mic 密封         | 密封堆栈中统一实现的垫片。 建议泡沫垫片 \> 70% 的压缩率 |
| Mic 可靠性     | 应该使用网格来防止灰尘和入口（在 PCB 下的出口麦克风之间并密封衬垫/顶盖） |
| Mic 隔离       | 橡胶垫片和振动通过结构分离，特别是由于集成的扬声器隔离任何振动路径 |
| 采样时钟      | 设备音频必须具有抖动和低偏差的下沉 |
| 记录功能   | 设备必须能够同时记录单个通道原始流 |
| USB                 | 所有 USB 音频输入设备必须根据[USB 音频设备 Rev3 规范](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)设置描述符 |
| 麦克风几何 | 驱动程序必须正确实现[麦克风数组几何描述符](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| 可发现性     | 设备不能将任何无法发现或不可控硬件、固件或基于第三方软件的非线性音频处理算法与设备一起使用 |
| 捕获格式      | 捕获格式必须使用 16 kHz 的最小采样速率和建议的24位深度 |

## <a name="electrical-architecture-considerations"></a>电气体系结构注意事项

在适用的情况下，阵列可以连接到 USB 主机（例如，运行 Microsoft 音频堆栈的 SoC），并将接口连接到语音服务或其他应用程序。

诸如 PDM 到 TDM 转换之类的硬件组件应确保在重新取样器中保留麦克风的动态范围和 SNR。

在任何音频 Mcu 中，都应支持高速 USB 音频类2.0，以便在更高的采样速率和比特深度为多达7个通道提供所需的带宽。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关语音设备 SDK 的详细信息](speech-devices-sdk.md)
