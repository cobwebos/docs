---
title: Azure Kinect DK 录制器
description: 了解如何使用 Azure Kinect 录制器将数据流从传感器 SDK 记录到文件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 录制, 播放, 读取器, matroska, mkv, 流, 深度, rgb, 相机, 彩色, imu, 音频
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73932222"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK 录制器

本文介绍如何使用 `k4arecorder` 命令行实用工具将传感器 SDK 发出的数据流录制到文件中。

>[!NOTE]
>Azure Kinect 录制器不会录制音频。

## <a name="recorder-options"></a>录制器选项

`k4arecorder` 提供不同的命令行参数用于指定输出文件和录制模式。

录制内容以 [Matroska.mkv 格式](record-file-format.md)存储。 录制内容为彩色和深度图像使用多个视频轨道，并包含相机校准和元数据等其他信息。

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>录制文件

示例 1。 录制深度 NFOV 非装箱 (640x576) 模式 RGB 1080p @ 30 fps（包括 IMU）内容。
按 **CTRL-C** 键可停止录制。

```
k4arecorder.exe output.mkv
```

示例 2。 录制 WFOV 非装箱 (1MP) RGB 3072p @ 15 fps（不包括 IMU）内容 10 秒。

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

示例 3。 录制 WFOV 2x2 装箱 30 fps 内容 5 秒，并保存到 output.mkv。

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>在录制之前，可以使用 [Azure Kinect 查看器](azure-kinect-viewer.md)配置 RGB 相机控件（例如设置手动白平衡）。

## <a name="verify-recording"></a>验证录制

可以使用 [Azure Kinect 查看器](azure-kinect-viewer.md)打开输出的 .mkv 文件。

若要提取轨道或查看文件信息，可以使用 [MKVToolNix](https://mkvtoolnix.download/) 工具包中的 `mkvinfo` 等工具。

## <a name="next-steps"></a>后续步骤

[将录制器与外部同步单元配合使用](record-external-synchronized-units.md)