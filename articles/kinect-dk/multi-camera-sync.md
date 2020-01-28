---
title: 跨多个 Azure Kinect DK 进行同步
description: 本文探讨多设备同步的优点，以及有关其执行方式的所有机密信息。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 01/10/2020
ms.topic: article
keywords: azure, kinect, 规格, 硬件, DK, 功能, 深度, 颜色, RGB, IMU, 麦克风, 阵列, 多, 同步
ms.openlocfilehash: 91703e86153f98e5a60e13a635f2ed48f66311ff
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279222"
---
# <a name="synchronization-across-multiple-azure-kinect-dk-devices"></a>跨多个 Azure Kinect DK 设备进行同步

本文探讨多设备同步的优点及其详细信息。

在开始之前，请务必查看 [Azure Kinect DK 硬件规格](hardware-specification.md)和[多相机硬件设置](https://support.microsoft.com/help/4494429)。 

开始多相机安装之前，需考虑几个重要事项。 

- 如果要控制每个设备的精确计时，建议使用手动曝光设置。 自动曝光允许每个彩色相机动态更改曝光，因此，两个设备之间的计时不可能完全保持相同。
- 使用主模式或从属模式时，针对图像报告的设备时间戳会将含义从“帧中心”变为“帧起始”。
- 避免不同相机之间出现 IR 相机干扰。 使用 ```depth_delay_off_color_usec``` 或 ```subordinate_delay_off_master_usec``` 来确保每种 IR 激光都在其自己的 160 微秒时间范围内触发，或者具有不同的视野。
- 确保使用最新的固件版本。
- 不要在图像捕获循环中重复设置相同的曝光设置。 
- 在需要时设置曝光，只调用 API 一次。


## <a name="why-to-use-multiple-azure-kinect-dk-devices"></a>为什么要使用多个 Azure Kinect DK 设备？

使用多个 Azure Kinect DK 设备的原因有很多。 下面是一些示例：
- 填充遮蔽区域
- 三维对象扫描 
- 提高有效帧速率，使之大于 30 FPS
- 对同一场景进行多个 4K 彩色图像的捕获，这些图像在从曝光起始点算起的 100 微秒范围内对齐
- 加大相机的空间覆盖范围

### <a name="solve-for-occlusion"></a>解决遮蔽问题

遮蔽是指你想要看的东西因存在某种阻碍而看不到。 在我们的示例中，Azure Kinect DK 设备有两台不共享同一原点的相机（深度相机和彩色相机），因此，一台相机可以看到另一台相机看不到的物体部分。 因此，将深度图像转换为彩色图像时，可能会在物体周围看到阴影。
在下图中，左相机可以看到灰色像素 P2，但从右相机到 P2 的光线会遇到白色前景物体。 因此，右相机无法看到 P2。

 ![遮蔽](./media/occlusion.png)

使用额外的 Azure Kinect DK 设备可以解决此问题并解决遮蔽区域的数据填充问题。

## <a name="set-up-multiple-azure-kinect-dk-devices"></a>设置多个 Azure Kinect DK 设备

请确保查看[多相机硬件设置文章](https://support.microsoft.com/help/4494429)，其中描述了硬件设置的不同选项。 

### <a name="synchronization-cables"></a>同步线缆

Azure Kinect DK 包含 3.5 毫米同步端口，可以用来将多个单元链接到一起。 链接后，相机可以协调深度相机和 RGB 相机的触发时间。 设备上专门配备了同步输入和同步输出端口，可以轻松组建菊花链。 兼容的线缆未随设备附送，必须单独购买。

线缆要求：

- 3.5 毫米公对公线缆（“3.5 毫米音频线”）
- 最大线缆长度应小于 10 米
- 支持立体声和单音线缆类型

在同步捕获中使用多个深度相机时，深度相机捕获应相互偏移 160 微秒或更多，以避免深度相机干扰。

> [!NOTE]
> 请确保移除外罩以显示同步端口。

### <a name="cross-device-calibration"></a>跨设备校准

在单设备深度和 RGB 相机中，使用工厂校准。 但在使用多个设备时，需考虑新的校准要求，确定如何将图像从捕获时所在的相机域转换为要在其中处理图像的相机域。
可以使用多个选项来交叉校准设备，但在 GitHub 绿屏代码示例中，我们使用 OpenCV 方法。可以使用多个选项来交叉校准设备，但在 [GitHub 绿屏代码示例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)中，我们使用 OpenCV 方法。

### <a name="usb-memory-on-ubuntu"></a>Ubuntu 上的 USB 内存

如果要在 Linux 上设置多相机同步，则默认情况下，仅为 USB 控制器分配 16 MB 的内核内存，用于处理 USB 传输。 通常情况下，该内存足够支持单个 Azure Kinect DK，但若要支持多个设备，则需更多内存。 若要增加内存，请执行以下步骤：
- 编辑 /etc/default/grub
- 将 GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" 行替换为 GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"。 在此示例中，我们将 USB 内存设置为 32 MB（二倍于默认值），但也可为其设置大得多的值。 选择适合解决方案的值。
- 运行 sudo update-grub
- 重启计算机

### <a name="verify-two-azure-kinect-dks-synchronization"></a>验证两个 Azure Kinect DK 的同步

在设置硬件并将主设备的同步输出端口连接到从属设备的同步输入端口后，可以使用 [Azure Kinect 查看器](azure-kinect-viewer.md)来验证设备设置。 也可对两个以上的设备执行此操作。

> [!NOTE]
> 从属设备是连接到“同步输入”引脚的设备。
> 主设备是连接到“同步输出”引脚的设备。

1. 获取每个设备的序列号。
2. 打开两个 [Azure Kinect 查看器](azure-kinect-viewer.md)实例
3. 首先打开从属 Azure Kinect DK 设备。 导航到 Azure Kinect 查看器，在“打开设备”部分选择从属设备：


      ![打开设备](./media/open-devices.png)

4. 在“外部同步”部分选择“从属”选项，然后启动此设备。 由于此设备在等待来自主设备的同步脉冲，因此在点击“启动”后，图像不会发送到从属设备。


      ![从属相机的启动](./media/sub-device-start.png)

5. 导航到 Azure Kinect 查看器的另一实例，打开主 Azure Kinect KD 设备。
6. 在“外部同步”部分选择“主”选项，然后启动此设备。

> [!NOTE]
> 主设备必须总是最后启动，才能在所有设备之间精确地进行图像捕获对齐。

启动 Azure Kinect 主设备时，应该会显示两个 Azure Kinect 设备中的已同步图像。

### <a name="avoiding-interference-from-other-depth-cameras"></a>避免来自其他深度相机的干扰

当深度传感器的 ToF 激光与另一深度相机的激光同时出现时，会发生干扰。
为了避免出现这种情况，那些感兴趣区域重叠的相机需要按“激光开启时间”进行时移，这样就不会同时开启激光。  每次捕获时，激光会开启 9 次，但处于活动状态的时间仅为 125 微秒，然后处于空闲状态 1450 微秒或2390 微秒，具体取决于操作模式。 因此，深度相机需要将其“激光开启时间”时移至少 125 微秒，并且该开启时间需要处于其他正在使用的深度传感器的空闲时间范围内。 

由于固件所用时钟和相机所用时钟之间存在差异，因此不能直接使用 125 微秒， 而只能使用 160 微秒，这是确保没有相机干扰所需的软件设置。 这样就可以将另外 9 个深度相机安排到 1450 微秒的 NFOV 空闲时间范围内。 确切的计时因所用深度模式而异。

根据[深度传感器原始计时表](hardware-specification.md)，曝光时间可按以下方式计算：

> [!NOTE]
> 曝光时间 = (IR 脉冲 * 脉冲宽度) + (空闲周期 * 空闲时间)

## <a name="triggering-with-custom-source"></a>使用自定义源进行触发

自定义同步源可用于替换主 Azure Kinect DK。 当图像捕获需要与其他设备同步时，这非常有用。 自定义触发器必须通过 3.5 毫米端口创建与主设备类似的同步信号。

- SYNC 信号为高电平有效信号，脉冲宽度应大于8 微秒。
- 频率支持必须精确到 30 fps、15 fps 和 5 fps，这是彩色相机的主 VSYNC 信号的频率。
- 来自板的 SYNC 信号应为 5 伏 TTL/CMOS，最大驱动容量不小于 8 毫安。
- 3\.5 毫米端口的所有样式（包括未绘图的“单音”类型）都可以与 Kinect DK 配合使用。 所有套筒和环都在 Kinect DK 内进行短路连接，它们连接到主 Azure Kinect DK 的地线。 尖端为同步信号。

![外部相机触发器信号](./media/resources/camera-trigger-signal.jpg)

## <a name="next-steps"></a>后续步骤

- [使用 Azure Kinect 传感器 SDK](about-sensor-sdk.md)
- [捕获 Azure Kinect 设备同步](capture-device-synchronization.md)
- [设置硬件](set-up-azure-kinect-dk.md)


