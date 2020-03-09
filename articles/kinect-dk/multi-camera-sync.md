---
title: 同步多个 Azure Kinect DK 设备
description: 本文探讨多设备同步的优势，以及如何设置要同步的设备。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure, kinect, 规格, 硬件, DK, 功能, 深度, 彩色, RGB, IMU, 阵列, 多, 同步
ms.openlocfilehash: 574537e7cf3887fc4c3c11597c9ad9f0c94869f8
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267569"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>同步多个 Azure Kinect DK 设备

每个 Azure Kinect DK 设备附带 3.5 毫米同步端口（**输入同步**和**输出同步**），可将多个设备链接在一起。 连接设备后，软件可以协调设备之间的触发定时。 

本文将介绍如何连接和同步设备。

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>使用多个 Azure Kinect DK 设备的好处

使用多个 Azure Kinect DK 设备的原因有很多，包括：

- 填补遮挡区域。 尽管 Azure Kinect DK 数据转换生成的是单个图像，但两个相机（深度和 RGB 相机）实际上保持着较小的一段距离。 这种偏移使得遮挡成为可能。 遮挡是指前景对象阻挡了设备上两个相机之一的背景对象的部分视角。 在生成的彩色图像中，前景对象看上去像是在背景对象上投射了一个阴影。  
   例如，在下图中，左侧相机可看到灰色像素“P2”。 但是，白色的前景对象阻挡了右侧相机的红外光束。 右侧相机无法获取“P2”的数据。  
   ![遮蔽](./media/occlusion.png)  
   附加的同步设备可以提供遮挡的数据。
- 扫描三维对象。
- 将有效帧速率提升至 30 帧/秒 (FPS) 以上的值。
- 捕获同一场景的多个 4K 彩色图像，所有图像在曝光开始后的 100 微秒 (&mu;s) 内对齐。
- 增大相机的空间覆盖范围。

## <a name="plan-your-multi-device-configuration"></a>规划多设备配置

在开始之前，请务必查看 [Azure Kinect DK 硬件规格](hardware-specification.md)和 [Azure Kinect DK 深度相机](depth-camera.md)。

### <a name="select-a-device-configuration"></a>选择设备配置

可使用以下任一方法来完成设备配置：

- **菊花链配置**。 同步一个主设备以及最多八个从属设备。  
   ![展示如何在菊花链配置中连接 Azure Kinect DK 设备的插图。](./media/multicam-sync-daisychain.png)
- **星形配置**。 同步一个主设备以及最多两个从属设备。  
   ![展示如何在星形配置中设置多个 Azure DK 设备的插图。](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>使用外部同步触发器

在两种配置中，主设备提供从属设备的触发信号。 但是，可将自定义的外部源用于同步触发器。 例如，可以使用此选项同步其他设备的图像捕获信号。 在菊花链配置或星形配置中，外部触发器源将连接到主设备。

外部触发器源的工作方式必须与主设备相同。 它必须提供一个具有以下特征的同步信号：

- 活动程度高
- 脉冲宽度：大于 8&mu;s
- 5V TTL/CMOS
- 最大驱动容量：不小于 8 毫安 (mA)
- 频率支持：精确到 30 FPS、15 FPS 和 5 FPS（彩色相机主 VSYNC 信号的频率）

触发器源必须使用 3.5 毫米音频线将信号传送到主设备的**输入同步**端口。 可以使用立体声或单声道音频线。 Azure Kinect DK 会将音频线连接器的所有套管和套环短接到一起，并将其接地。 如下图所示，设备只从连接器尖端接收同步信号。

![外部触发器信号的线缆配置](./media/resources/camera-trigger-signal.jpg)

有关如何使用外部设备的详细信息，请参阅[将 Azure Kinect 录制器与外部同步设备配合使用](record-external-synchronized-units.md)

### <a name="plan-your-camera-settings-and-software-configuration"></a>规划相机设置和软件配置

有关如何设置软件来控制相机以及如何使用图像数据的信息，请参阅 [Azure Kinect 传感器 SDK](about-sensor-sdk.md)。

本部分讨论影响已同步设备（而不是单独的设备）的多种因素。 软件应考虑到这些因素。

#### <a name="exposure-considerations"></a>曝光考虑因素
若要控制每个设备的精确定时，我们建议使用手动曝光设置。 使用自动曝光设置时，每个彩色相机可能会动态更改实际曝光。 由于曝光会影响定时，此类更改很快就会使相机失去同步。

避免在图像捕获循环中重复指定相同的曝光设置。 必要时，请只调用 API 一次。

#### <a name="timestamp-considerations"></a>时间戳考虑因素
充当主角色或从属角色的设备根据“帧起点”而不是“帧中点”来报告图像时间戳。  

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>避免多个深度相机之间产生干扰

如果多个深度相机对重叠的视场成像，每个相机必须对其自身关联的激光成像。 为了防止激光相互干扰，相机捕获应相互偏离 160μs 或以上。

对于每次深度相机捕获，激光会打开 9 次，每次只保持活动状态 125&mu;s。 然后，激光将空闲 14505&mu;s 或 23905&mu;s，具体取决于工作模式。 此行为意味着，偏移量计算的起点为 125&mu;s。

此外，相机时钟与设备固件时钟之差会将最小偏移量增大至 160&mu;s。 若要根据配置计算出更精确的偏移量，请注意所用的深度模式，并参考[深度传感器原始定时表](hardware-specification.md#depth-sensor-raw-timing)。 参考此表中的数据可以使用以下公式计算最小偏移量（每个相机的曝光时间）：

> 曝光时间 = (红外脉冲 &times; 脉冲宽度) + (空闲周期 &times; 空闲时间)     

使用 160&mu;s 偏移量时，最多可以配置 9 个额外的深度相机，以便在每束激光打开时，其他激光保持空闲状态。

在软件中，使用 ```depth_delay_off_color_usec``` 或 ```subordinate_delay_off_master_usec``` 来确保每束红外激光在其自身的 160&mu;s 时限内触发，或者提供不同的视场。

## <a name="prepare-your-devices-and-other-hardware"></a>准备设备和其他硬件

除了配置多个 Azure Kinect DK 设备以外，可能还需要获得其他主机和其他硬件才能支持你要构建的配置。 在开始设置之前，请使用本部分中的信息来确保所有设备和硬件已准备就绪。

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK 设备

对于要同步的每个 Azure Kinect DK 设备，请采取以下措施：

- 确保设备上已安装最新的固件。 有关如何更新设备的详细信息，请参阅[更新 Azure Kinect DK 固件](update-device-firmware.md)。 
- 卸下设备护盖，露出同步端口。
- 记下每个设备的序列号。 在稍后的设置过程中需使用此编号。

### <a name="host-computers"></a>主机

每个 Azure Kinect DK 通常使用自身的主机。 可以根据设备的使用方式以及通过 USB 连接传输的数据量使用专用主机控制器。 

确保每台主机上安装了 Azure Kinect 传感器 SDK。 有关如何安装传感器 SDK 的详细信息，请参阅[快速入门：设置 Azure Kinect DK](set-up-azure-kinect-dk.md)。 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 计算机：Ubuntu 上的 USB 内存

默认情况下，基于 Linux 的主机只为 USB 控制器分配 16 MB 的内核内存用于处理 USB 传输。 此内存量通常足以支持单个 Azure Kinect DK。 但是，若要支持多个设备，USB 控制器必须有更多的内存。 若要增大内存，请执行以下步骤：

1. 编辑 **/etc/default/grub**。
1. 找到以下行：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   替换为以下行：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 这些命令会将 USB 内存设置为 32 MB。 此示例设置是默认值的两倍。 可以设置一个大得多的值，只要适合解决方案即可。
1. 运行 **sudo update-grub**。
1. 重新启动计算机。

### <a name="cables"></a>线缆

若要将设备相互连接并连接到主机，必须使用 3.5 毫米公对公线缆（也称为 3.5 毫米音频线）。 线缆长度应小于 10 米，可以是立体声或单声道音频线。

所需的线缆数取决于所用的设备数以及具体的设备配置。 Azure Kinect DK 机箱中未随附线缆。 必须单独购买线缆。

如果在星形配置中连接设备，则还需要一个耳机分线器。

## <a name="connect-your-devices"></a>连接数据

**在菊花链配置中连接 Azure Kinect DK 设备**

1. 将每个 Azure Kinect DK 连接到电源。
1. 将每个设备连接到其自身的主机。 
1. 选择一个设备充当主设备，并将 3.5 毫米音频线插入该设备的**输出同步**端口。
1. 将该线缆的另一端插入第一个从属设备的**输入同步**端口。
1. 若要连接另一个设备，请将另一根线缆插入第一个从属设备的**输出同步**端口，以及下一个设备的**输入同步**端口。
1. 重复上述步骤，直到所有设备都已连接。 最后一个设备应连接了一根线缆。 其**输出同步**端口应该是空的。

**在星形配置中连接 Azure Kinect DK 设备**

1. 将每个 Azure Kinect DK 连接到电源。
1. 将每个设备连接到其自身的主机。 
1. 选择一个设备充当主设备，将耳机分线器的单体端插入其**输出同步**端口。
1. 将 3.5 毫米音频线连接到耳机分线器的“分接”端。
1. 将每根线缆的另一端插入某个从属设备的**输入同步**端口。

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>验证设备是否已连接并可通信

若要验证设备是否已正确连接，请使用 [Azure Kinect 查看器](azure-kinect-viewer.md)。 根据需要重复此过程，以结合主设备测试每个从属设备

> [!IMPORTANT]  
> 在测试过程中，必须知道每个 Azure Kinect DK 的序列号。

1. 打开 Azure Kinect 查看器的两个实例。
1. 在“打开设备”下，选择要测试的从属设备的序列号。   
   ![打开设备](./media/open-devices.png)
   > [!IMPORTANT]  
   > 若要使所有设备的图像捕获保持精确同步，必须最后启动主设备。  
1. 在“外部同步”下，选择“从属设备”。    
   ![启动从属相机](./media/sub-device-start.png)
1.  选择“开始”  。  
    > [!NOTE]  
    > 由于这是一个从属设备，在设备启动后，Azure Kinect 查看器不会显示图像。 在从属设备收到主设备发出的同步信号之前不会显示图像。
1. 启动从属设备后，使用 Azure Kinect 查看器的另一实例打开主设备。
1. 在“外部同步”下，选择“主设备”。  
1. 选择“开始”  。

Azure Kinect 主设备启动后，Azure Kinect 查看器的两个实例应显示图像。

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>根据同步设置校准设备

验证设备可正确通信后，接下来可对其进行校准，以便在单个域中生成图像。

在单个设备中，深度相机和 RGB 相机已经过出厂校准，可以协同工作。 但是，如果必须一同使用多个设备，则必须对这些设备进行校准，以确定如何将图像从捕获它时所在的相机域转换为用于处理图像的相机域。

可以使用多个选项来交叉校准设备。 Microsoft 提供了使用 OpenCV 方法的 [GitHub 绿屏代码示例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)。 此代码示例的自述文件提供了有关校准设备的更多详细信息和说明。

有关校准的详细信息，请参阅[使用 Azure Kinect 校准功能](use-calibration-functions.md)。

## <a name="next-steps"></a>后续步骤

设置已同步的设备后，还可以了解如何使用
> [!div class="nextstepaction"]
> [Azure Kinect 传感器 SDK 录制和播放 API](record-playback-api.md)

## <a name="related-topics"></a>相关主题

- [关于 Azure Kinect 传感器 SDK](about-sensor-sdk.md)
- [Azure Kinect DK 硬件规格](hardware-specification.md) 
- [快速入门：设置 Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [更新 Azure Kinect DK 固件](update-device-firmware.md) 
- [重置 Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect 查看器](azure-kinect-viewer.md) 
