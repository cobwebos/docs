---
title: 使用 Azure Kinect 传感器 SDK 录制文件格式
description: 了解如何使用 Azure Kinect 传感器 SDK 录制的文件格式。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect, azure, 传感器, sdk, 深度, rgb, 录制, 播放, matroska, mkv
ms.openlocfilehash: d0f7653afe3cc92e059b2615ebef18312faa716b
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73932207"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>使用 Azure Kinect 传感器 SDK 录制文件格式

若要录制传感器数据，需使用 Matroska (.mkv) 容器格式，以便可以存储多个轨道。
使用多种多样的编解码器。 录制文件包含用于存储颜色、深度、IR 图像和 IMU 的轨道。

在 [Matroska 网站](https://www.matroska.org/index.html)上可以找到 .mkv 容器格式的大致详细信息。

| 轨道名称 | 编解码器格式                          |
|------------|---------------------------------------|
| COLOR      | 与模式相关（MJPEG、NV12 或 YUY2） |
| DEPTH      | b16g（16 位灰度，大字节序）   |
| IR         | b16g（16 位灰度，大字节序）   |
| IMU        | 自定义结构。请参阅下面的 [IMU 示例结构](record-file-format.md#imu-sample-structure)。 |

## <a name="using-third-party-tools"></a>使用第三方工具

可以使用 `ffmpeg` 等工具或者 [MKVToolNix](https://mkvtoolnix.download/) 工具包中的 `mkvinfo` 命令查看和提取录制文件中的信息。

例如，以下命令将深度轨道作为 16 位 PNG 序列提取到同一个文件夹：

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

`-map 0:1` 参数将提取轨道索引 1，对于大多数录制内容而言，此索引是深度。 如果录制内容不包含颜色轨道，则会使用 `-map 0:0`。

`-vsync 0` 参数强制 ffmpeg 按原样提取帧，而不是尝试匹配 30 fps、15 fps 或 5 fps 的帧速率。

## <a name="imu-sample-structure"></a>IMU 示例结构

如果在不使用播放 API 的情况下从文件中提取 IMU 数据，则数据将采用二进制格式。
下面是 IMU 数据的结构。 所有字段均为小字节序。

| 字段                        | 类型     |
|------------------------------|----------|
| 加速度传感器时间戳 (µs) | uint64   |
| 加速度传感器数据 (x, y, z) | float[3] |
| 陀螺仪时间戳 (µs)     | uint64   |
| 陀螺仪数据 (x, y, z)     | float[3] |

## <a name="identifying-tracks"></a>识别轨道

可能需要识别哪个轨道包含颜色、深度、IR 等属性。 使用第三方工具读取 Matroska 文件时，需要识别轨道。
轨道编号根据相机模式和已启用的轨道集而异。 标记用于标识每个轨道的含义。

下面所列的每个标记将附加到特定的 Matroska 元素，可用于查找相应的轨道或附件。

可以使用 `ffmpeg` 和 `mkvinfo` 等工具查看这些标记。
[录制和播放](record-playback-api.md)页上提供了完整的标记列表。

| 标记名称             | 标记目标             | 标记值             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | 颜色轨道            | Matroska 轨道 UID    |
| K4A_DEPTH_TRACK      | 深度轨道            | Matroska 轨道 UID    |
| K4A_IR_TRACK         | IR 轨道               | Matroska 轨道 UID    |
| K4A_IMU_TRACK        | IMU 轨道              | Matroska 轨道 UID    |
| K4A_CALIBRATION_FILE | 校准附件 | 附件文件名   |

## <a name="next-steps"></a>后续步骤

[录制和播放](record-playback-api.md)
