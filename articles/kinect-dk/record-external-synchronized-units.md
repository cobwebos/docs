---
title: 将 Azure Kinect 录制器与外部同步单元配合使用
description: 将 Azure Kinect 录制器与外部同步单元配合使用
author: joylital
ms.author: joylital
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, 传感器, 查看器, 外部同步, 相位延迟, 深度, RGB, 相机, 音频线, 录制器
ms.openlocfilehash: 0b9d61aead44ffa55fdb32998a89ceade7d4f412
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876782"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-units"></a>将 Azure Kinect 录制器与外部同步单元配合使用

本文提供有关 [Azure Kinect 录制器](azure-kinect-recorder.md)如何结合配置的外部同步设备记录数据的指导。

## <a name="prerequisites"></a>先决条件

- [设置多个 Azure Kinect DK 单元进行外部同步](https://support.microsoft.com/help/4494429)。

## <a name="external-synchronization-constraints"></a>外部同步约束

- 主设备不能连接 SYNC IN 线缆。
- 主设备必须流式传输 RGB 相机数据才能启用同步。
- 所有单元必须使用相同的相机配置（帧速率和分辨率）。
- 所有单元必须运行相同的设备固件（[更新固件](update-device-firmware.md)说明）。
- 必须先启动所有从属设备，然后启动主设备。
- 应在所有设备上设置相同的曝光值。
- 每个从属设备的“主控延迟关闭”设置相对于主设备。 

## <a name="record-when-each-unit-has-a-host-pc"></a>当每个单元都有一台主机电脑时进行录制

在以下示例中，每个设备都有自身专用的主机电脑。
我们建议将设备连接到专用电脑，以防止 USB 带宽和 CPU/GPU 使用率出现问题。

### <a name="subordinate-1"></a>Subordinate-1

1. 设置第一个单元的录制器

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. 设备开始等待

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Subordinate-2

1. 设置第二个单元的录制器

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. 设备开始等待

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>主设备

1. 在主设备上开始录制

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. 等到录制完成

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>当多个单元连接到单个主机电脑时进行录制

可将多个 Azure Kinect DK 连接到单个主机电脑。 但是，这可能需要满足很高的 USB 带宽和主机算力要求。 若要降低需求：

- 请将每个设备连接到其自身的 USB 主控制器。
- 使用强大的 GPU，它可以处理每个设备的深度引擎。
- 仅记录所需的传感器，并使用较低的帧速率。

始终先启动从属设备，最后再启动主设备。

## <a name="subordinate-1"></a>Subordinate-1

1. 在从属设备上启动录制器

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. 设备进入等待状态

## <a name="master"></a>主设备

1. 启动主设备

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. 等待录制完成

## <a name="playing-recording"></a>播放录制内容

可以使用 [Azure Kinect 查看器](azure-kinect-viewer.md)播放录制内容。



## <a name="tips"></a>提示

- 使用手动曝光在同步相机上进行录制。 RGB 相机自动曝光可能会影响时间同步。
- 重启从属设备会导致失去同步。
- 某些[相机模式](hardware-specification.md#depth-camera-supported-operating-modes)最大支持 15 fps 的帧速率。我们建议不要在各个设备上使用不同的模式/帧速率
- 将多个单元连接到单个电脑很容易使 USB 带宽达到饱和，请考虑为每个设备使用单独的主机电脑。 另请注意 CPU/GPU 算力。
- 如果不需要使用麦克风和 IMU 来提高可靠性，请将其禁用。

如有任何问题，请参阅[故障排除](troubleshooting.md)

## <a name="see-also"></a>另请参阅

- [设置外部同步](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Azure Kinect 录制器](azure-kinect-recorder.md)的设置及其他信息。
- 使用 [Azure Kinect 查看器](azure-kinect-viewer.md)播放录制内容，或者设置无法通过录制器使用的 RGB 相机属性。
- 使用 [Azure Kinect 固件工具](azure-kinect-firmware-tool.md)更新设备固件。
