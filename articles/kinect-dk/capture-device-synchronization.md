---
title: 捕获 Azure Kinect 设备同步
description: 了解如何使用 Azure Kinect 传感器 SDK 同步 Azure Kinect 捕获设备。
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 深度, rgb, 内部, 外部, 同步, 菊花链, 相位偏移
ms.openlocfilehash: 914a29882277c11c388d3f81f0b1fe827fc2f156
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73932209"
---
# <a name="capture-azure-kinect-device-synchronization"></a>捕获 Azure Kinect 设备同步

Azure Kinect 硬件可以调整彩色和深度图像的捕获时间。 同一设备上的相机之间的调整属于**内部同步**。 跨多个连接设备的捕获时间调整属于**外部同步**。

## <a name="device-internal-synchronization"></a>设备内部同步

各个相机之间的图像捕获在硬件中同步。 在包含来自彩色和深度传感器的图像的每个 [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) 中，图像的时间戳根据硬件的运行模式进行调整。 默认情况下，捕获的图像是调整的曝光的中心。 可以使用 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 的 `depth_delay_off_color_usec` 字段调整深度和彩色捕获的相对计时。

## <a name="device-external-synchronization"></a>设备外部同步

有关硬件设置，请参阅[设置外部同步](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)。

每个连接设备的软件必须配置为以**主**模式或**从属**模式运行。 在 [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 中配置此设置。

使用外部同步时，始终应该先启动从属相机，然后再启动主相机，这样才能正常调整时间戳。

### <a name="subordinate-mode"></a>从属模式

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>主模式

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>检索同步插孔状态

若要以编程方式检索同步输入和同步输出插孔的当前状态，请使用 [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) 函数。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何启用和捕获设备同步。 你还可以查看如何使用 

>[!div class="nextstepaction"]
>[Azure Kinect 传感器 SDK 录制和播放 API](record-playback-api.md)
