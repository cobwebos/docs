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
ms.openlocfilehash: e39056d6ed1ced85e3f953bcbd7f04dd6311942c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111659"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>语音设备 SDK 麦克风阵列建议

本文介绍如何为语音设备 SDK 设计麦克风阵列。

语音设备 SDK 最适合与根据以下准则设计的麦克风阵列（包括麦克风几何结构和组件选择）配合使用。 本文还提供了有关集成和电力注意事项的指导。

## <a name="microphone-geometry"></a>麦克风几何结构

建议将以下阵列几何结构用于 Microsoft 音频堆栈。 借助更多的麦克风以及与特定应用程序、用户方案和设备外形规格之间的依赖关系，可以改善音源定位和环境噪声的抑制。

|     | 环形阵列 |     | 线性阵列 |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| 麦克风数目 | 7 | 4 | 4 | 2 |
| 几何结构 | 6 个外置，1 个中置，半径 = 42.5 毫米，均匀排布间距 | 3 个外置，1 个中置，半径 = 42.5 毫米，均匀排布间距 | 长度 = 120 毫米，间距 = 40 毫米 | 间距 = 40 毫米 |

应根据上述每个阵列的编号（从 0 开始递增）排列麦克风声道的顺序。 需要为 Microsoft 音频堆栈提供额外的音频播放参考流才能让它执行回声消除。

## <a name="component-selection"></a>组件选择

应选择适当的麦克风组件来准确重现无噪声和失真的信号。

选择麦克风时建议的属性如下：

| 参数 | 建议 |
| --------- | ----------- |
| 信噪比 | \>= 65 dB（1 kHz 信号，94 dBSPL，A 加权噪声） |
| 振幅匹配 | ± 1 dB @ 1 kHz |
| 相位匹配 | ± 2° @ 1 kHz |
| 声学过载点 (AOP) | \>= 120 dBSPL (THD = 10%) |
| 比特率 | 最小 24 位 |
| 采样率 | 最小 16 kHz\* |
| 频率响应 | ± 3 dB，200-8000 Hz 浮动掩码\* |
| 可靠性 | 存储温度范围-40 ° C 到70° C<br />工作温度范围：-20°C 到 55°C |

_高质量通信（VoIP）应用程序可能需要 \*更高的采样率或 "更宽" 频率范围_

必须搭配良好的电声集成选择适当的组件，以免降低所用组件的性能。 独特的用例还可能需要满足额外的要求（例如工作温度范围）。

## <a name="microphone-array-integration"></a>麦克风阵列集成

集成到设备后，麦克风阵列的性能将与组件规格不同。 必须确保在集成后适当匹配麦克风。 因此，在经过任何固定增益或均衡 (EQ) 之后测量的设备性能应符合以下建议：

| 参数          | 建议                                        |
| ------------------ | -------------------------------------------------- |
| 信噪比                | \> 63 dB（1 kHz 信号，94 dBSPL，A 加权噪声） |
| 输出灵敏度 | -26 dBFS/Pa @ 1 kHz（建议）                  |
| 振幅匹配 | ± 2 dB，200-8000 Hz                                |
| 总谐波失真率\*             | ≤ 1%，200-8000 Hz，94 dBSPL，5 阶             |
| 频率响应 | ± 6 dB，200-8000 Hz 浮动掩码\*\*              |

\*\*_需要较低的扭曲音箱来度量 THD （例如 NEUMANN KH120）_

_高质量通信（VoIP）应用程序可能需要 \*\*"更宽" 频率范围_

## <a name="speaker-integration-recommendations"></a>扬声器集成建议

由于包含扬声器的语音识别设备需要回声消除，因此我们在扬声器选择和集成方面提供了附加的建议。

| 参数 | 建议 |
| --------- | ----------- |
| 线性注意事项 | 在扬声器参考信号后面不要进行非线性处理，否则需要基于硬件的环回参考流 |
| 扬声器环回 | 通过 WASAPI、专用 API、自定义 ALSA 插件 (Linux) 提供，或通过固件通道提供 |
| 总谐波失真率 | 第三个 Octave 区段最少5个订单，70 dBA 播放 @ 0.8 m ≤6.3%，315-500 Hz ≤5%，630-5000 Hz |
| 麦克风回声耦合 | \> -10 dB TCLw，使用 ITU-T G.122 Annex B.4 方法，规范化为麦克风水平<br />TCLw = TCLwmeasured \+ (测量水平 - 目标输出灵敏度)<br />TCLw = TCLwmeasured \+ (测量水平 - (-26)) |

## <a name="integration-design-architecture"></a>集成设计体系结构

将麦克风集成到设备时，需要遵守以下体系结构方面的准则：

| 参数 | 建议 |
| --------- | -------------- |
| 麦克风端口相似性 | 阵列中的所有麦克风端口具有相同的长度 |
| 麦克风端口尺寸 | 端口大小：Ø0.8-1.0 毫米 端口长度/端口直径：\< 2 |
| 麦克风密封性         | 在堆栈中使用统一的密封垫片。 建议对泡沫垫片实施 \> 70% 的压缩率 |
| 麦克风可靠性     | 应使用滤网来阻挡灰尘和入口污物（安装在端口位于底部的麦克风的 PCB 与密封垫片/顶盖之间） |
| 麦克风隔离       | 在结构中安装橡胶垫片和振动解耦装置，专门用于隔离集成扬声器后出现的任何振动路径 |
| 采样时钟      | 设备音频不可出现低偏差的抖动和断续 |
| 录制功能   | 设备必须能够同时录制单个原声道流 |
| USB                 | 所有 USB 音频输入设备必须根据 [USB 音频设备修订版 3 规范](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)设置描述符 |
| 麦克风几何结构 | 驱动程序必须正确实现[麦克风阵列几何描述符](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| 可发现性     | 设备中不能包含任何不可发现或不可控的硬件、固件或第三方基于软件的非线性音频处理算法 |
| 捕获格式      | 捕获格式必须使用 16 kHz 的最小采样速率和建议的24位深度 |

## <a name="electrical-architecture-considerations"></a>电力体系结构注意事项

在适用的情况下，阵列可以连接到 USB 主机（例如，运行 Microsoft 音频堆栈的 SoC），并可与语音服务或其他应用程序对接。

硬件组件（例如 PDM-TDM 转换组件）应确保在再采样器中保留麦克风的动态范围和信噪比。

高速 USB 音频类 2.0 应在任何音频 MCU 中受支持，以便在使用较高采样率和位深度时，为最多 7 个声道提供所需的带宽。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关语音设备 SDK 的详细信息](speech-devices-sdk.md)
