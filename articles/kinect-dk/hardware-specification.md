---
title: Azure Kinect DK 硬件规格
description: 了解 Azure Kinect DK 的组件、规范和功能。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azure, kinect, 规格, 硬件, DK, 功能, 深度, 颜色, RGB, IMU, 麦克风, 阵列
ms.openlocfilehash: 32b6c2d840c602a04e12a77004f5d981cdaca911
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931717"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Azure Kinect DK 硬件规格 

本文详细说明 Azure Kinect 硬件如何将 Microsoft 的最新传感器技术集成到单个已连接 USB 的附件。

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>术语

整篇文章使用了以下缩写术语。

* NFOV（窄视场深度模式）
* WFOV（宽视场深度模式）
* FOV（视场）
* FPS（每秒帧数）
* IMU（惯性测量单元）

## <a name="product-dimensions-and-weight"></a>产品尺寸和重量

Azure Kinect 设备的尺寸和重量如下。

- **维度**：103 x 39 x 126 毫米
- **重量**：440 克

![Azure Kinect DK 尺寸](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>工作环境

Azure Kinect DK 适用于在以下环境条件下工作的开发人员和商业单位：

- **温度**：10-25⁰C
- **湿度**：8-90%（非冷凝）相对湿度

> [!NOTE]
> 不符合上述环境条件可能会导致设备出现故障和/或不正常运行。 这些环境条件适用于设备在所有工作条件下运行时的邻近周边环境。 如果配合外部机箱使用，我们建议使用有效的温度控制和/或其他散热解决方案，来确保设备工作条件保持在这些范围内。 本设备在前端型面与后端套管之间设计了一个散热通道。 使用本设备时，请确保不要阻挡此散热通道。

请参阅附加的产品[安全信息](https://support.microsoft.com/help/4023454/safety-information)。

## <a name="depth-camera-supported-operating-modes"></a>深度相机支持的工作模式

Azure Kinect DK 集成了 Microsoft 设计的 1 兆像素时差测距 (ToF) 深度相机，该相机使用[符合 ISSCC 2018 的图像传感器](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018)。 深度相机支持如下所述的模式：

 | Mode            | 解决方法 | FOI       | FPS                | 工作范围* | 曝光时间 |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV 非装箱   | 640x576    | 75°x65°   | 0、5、15、30       | 0.5 - 3.86 米       | 12.8 毫秒        |
| NFOV 2x2 装箱 (SW) | 320x288    | 75°x65°   | 0、5、15、30       | 0.5 - 5.46 米       | 12.8 毫秒        |
| WFOV 2x2 装箱 | 512x512    | 120°x120° | 0、5、15、30       | 0.25 - 2.88 米      | 12.8 毫秒        |
| WFOV 非装箱   | 1024x1024  | 120°x120° | 0、5、15           | 0.25 - 2.21 米      | 20.3 毫秒        |
| 被动 IR      | 1024x1024  | 不适用       | 0、5、15、30       | 不适用              | 1.6 毫秒         |

\*850nm 时 15% 到 95% 的反射率，2.2 μW/cm<sup>2</sup>/nm，随机误差标准偏差 ≤ 17 mm，典型系统误差 < 11 mm + 0.1% 的距离（无多路径干扰）。 超出指示范围的深度取决于对象反射率。

## <a name="color-camera-supported-operating-modes"></a>彩色相机支持的工作模式

Azure Kinect DK 包含 OV12A10 12MP CMOS 滚动快门传感器。 下面列出了本机工作模式：

|             RGB 相机分辨率 (HxV)  |          纵横比  |          格式选项   |          帧速率 (FPS)  |          额定 FOV (HxV)（处理后）  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0、5、15、30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0、5、15、30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0、5、15           |          90°x74.3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0、5、15、30       |          90°x74.3°                            |

RGB 相机与 USB 视频类兼容，可以在未安装传感器 SDK 的情况下使用。 RGB 相机颜色空间：BT.601 全范围 [0..255]。 

> [!NOTE]
> 传感器 SDK 能够以 BGRA 像素格式提供彩色图像。 这并非设备支持的本机模式，如果使用，会导致 CPU 负载增大。 主机 CPU 用于转换从设备收到的 MJPEG 图像。

## <a name="rgb-camera-exposure-time-values"></a>RGB 相机曝光时间值

以下是可接受的 RGB 相机手动曝光值的映射：

| exp| 2^exp | 50Hz   |60Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="camera-field-of-view"></a>相机视场

下图显示了深度和 RGB 相机视场（传感器“看到”的视角）。 下图显示了 4:3 模式的 RGB 相机。

![相机 FOV](./media/resources/hardware-specs-media/camera-fov.png)

此图显示了相机在正面 2000 mm 距离的视场。

![相机 FOV 正面视场](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> 如果深度采用 NFOV 模式，RGB 相机在 4:3 模式下的像素重叠性能优于 16:9 模式。

## <a name="motion-sensor-imu"></a>运动传感器 (IMU)

嵌入式惯性测量单元 (IMU) 为 LSM6DSMUS，包含加速度传感器和陀螺仪。 加速度传感器和陀螺仪同时按 1.6 kHz 采样。 样本以 208 Hz 的频率报告给主机。

## <a name="microphone-array"></a>麦克风阵列

Azure Kinect DK 中嵌入了被视为标准 USB 音频类 2.0 设备的优质七麦克风环形阵列。 可以访问所有 7 个通道。 性能规格：

- 灵敏度：-22 dBFS（94 dB SPL，1 kHz）
- 信噪比 > 65 dB
- 声学过载点：116 dB

![麦克风气泡图](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK 是一个 USB3 组合设备，它会向操作系统公开以下硬件终结点：

供应商 ID 为 0x045E (Microsoft)，产品 ID 如下表所示：

|    USB 接口        |    PNP IP    |     说明            |
|-------------------------|--------------|----------------------|
|    USB3.1 第 1 代集线器    |    0x097A    |    主集线器    |
|    USB2.0 集线器         |    0x097B    |    HS USB          |
|    深度相机       |    0x097C    |    USB3.0            |
|    彩色相机       |    0x097D    |    USB3.0            |
|    麦克风        |    0x097E    |    HS USB          |

## <a name="indicators"></a>指示灯

设备的正面配备了相机流指示灯，可以使用传感器 SDK 以编程方式将其禁用。

设备背面的状态 LED 指示设备状态：

| 指示灯状态     | 表示                                                   |
|-----------------------|------------------------------------------------------------|
| 稳定白色           | 设备已打开并正常工作。                         |
| 闪烁白色        | 设备已打开，但尚未建立 USB 3.0 数据连接。   |
| 闪烁琥珀色        | 设备电量不足，无法正常运行。               |
| 交替闪烁琥珀色和白色  | 正在进行固件更新或恢复                    |

## <a name="power-device"></a>设备供电

可通过两种方式为设备供电：

1. 使用随附的电源。 通过单独的 USB Type-C 转 Type-A 线缆连接数据端口。
2. 使用 Type-C 转 Type-C 线缆供电和传输数据。

Azure Kinect DK 未随附 Type-C 转 Type-C 线缆。

> [!NOTE]
> - 随附的电源线是 USB Type-A 转桶形单柱连接器。 使用此线缆时请结合随附的墙上电源。 两个标准 USB Type-A 端口提供的电量并不足以满足本设备的消耗。
> - USB 线缆非常重要，我们建议使用优质线缆，并在远程部署本单元之前验证功能。

> [!TIP]
> 选择良好的 Type-C 转 Type-C 线缆：
> - [USB 认证的线缆](https://www.usb.org/products)必须支持供电和数据传输。
> - 无源线缆的长度应小于 1.5 米。 如果更长，请使用有源线缆。 
> - 线缆需要能够支持 1.5A 以上的电流。 否则，需要连接外部电源。

检查线缆：

- 使用线缆将设备连接到主机电脑。
- 验证所有设备是否在 Windows 设备管理器中正确列出。 深度相机和 RGB 相机应如以下示例中所示列出。

  ![设备管理器中的 Azure Kinect DK](./media/resources/hardware-specs-media/device-manager.png)

- 在 Azure Kinect 查看器中使用以下设置，验证线缆是否能够可靠地流式传输所有传感器的数据：

  - 深度相机：NFOV 非装箱
  - RGB 相机：2160p
  - 麦克风和 IMU 已启用

## <a name="power-consumption"></a>功耗

Azure Kinect DK 的最大功率为 5.9W；具体的功耗与用例相关。

## <a name="calibration"></a>校准

Azure Kinect DK 在出厂之前已校准。 可通过传感器 SDK 以编程方式查询视觉传感器和惯性传感器的校准参数。

## <a name="external-synchronization"></a>外部同步

设备包含 3.5 mm 同步插孔，可将多个单元链接到一起。 链接后，相机可以协调深度相机和 RGB 相机的触发时间。 设备上专门配备了内同步和外同步插孔，可以轻松组建菊花链。 兼容的线缆未随设备附送，必须单独购买。

线缆要求：

- 3.5-mm 插针式公对公线缆（“3.5-mm 音频线”）
- 最大线缆长度 < 10 米
- 支持立体声和单音线缆

在同步捕获中使用多个深度相机的线缆时，深度相机捕获应相互偏移 160us 或更多，以避免深度相机干扰。

有关[外部同步设置](https://support.microsoft.com/help/4494429)的更多详细信息

## <a name="device-recovery"></a>设备恢复

可以使用定位销下方的按钮将设备固件重置为原始固件。

![Azure Kinect DK 恢复按钮](./media/resources/hardware-specs-media/recovery.png)

若要恢复设备，请参阅[此处的说明](https://support.microsoft.com/help/4494277)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Kinect 传感器 SDK](about-sensor-sdk.md)
- [设置硬件](set-up-azure-kinect-dk.md)
