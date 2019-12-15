---
title: Azure Kinect 人体跟踪 SDK 下载
description: 了解如何在 Windows 或 Linux 中下载每个版本的 Azure Kinect 传感器 SDK。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, 下载更新, 最新, 可用, 安装, 人体, 跟踪
ms.openlocfilehash: de2910408d817b5f0eb21d3af4b0f4473182253b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928863"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>下载 Azure Kinect 人体跟踪 SDK

本文档提供各版本 Azure Kinect 正文跟踪 SDK 的安装链接。

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect 人体跟踪 SDK 的内容

- 标头和库，用于通过 Azure Kinect DK 生成人体跟踪应用程序。
- 使用 Azure Kinect DK 的人体跟踪应用程序所需的可分发 DLL。
- 示例人体跟踪应用程序。

## <a name="windows-download-links"></a>Windows 下载链接

版本       | 下载
--------------|----------
0.9.5 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100636) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100415) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100307) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100128) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100063) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=58402) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Linux 安装说明

目前，唯一支持的分发版是 Ubuntu 18.04。 若要请求对其他分发版的支持，请参阅[此页](https://aka.ms/azurekinectfeedback)。

首先，需要遵照[此处](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software)的说明，配置 [Microsoft 的包存储库](https://packages.microsoft.com/)。

`libk4abt<major>.<minor>-dev` 包中包含标头以及要针对 `libk4abt` 生成的 CMake 文件。
`libk4abt<major>.<minor>` 包中包含运行依赖于 `libk4abt` 的可执行文件以及示例查看器所需的共享对象。

基本教程需要 `libk4abt<major>.<minor>-dev` 包。 若要安装该包，请运行

`sudo apt install libk4abt0.9-dev`

如果该命令成功，则表示 SDK 可供使用。

> [!NOTE]
> 安装 SDK 时，请记住要安装到的路径。 例如，“C:\Program Files\Azure Kinect Body Tracking SDK 0.9.5”。 你将要在此路径中查找文章中参考的示例。

## <a name="change-log"></a>更改日志

### <a name="v095"></a>v0.9.5
* [功能] C# 支持。 C# 包装器已打包在 nuget 包中。
* [功能] 多跟踪器支持 允许创建多个跟踪器。 现在，用户可以创建多个跟踪器，以从不同的 Azure Kinect 设备跟踪人体。
* [功能] CPU 模式的多线程处理支持。 在 CPU 模式下运行时，将使用所有核心来最大程度地提高速度。
* [功能] 将 `gpu_device_id` 添加到 `k4abt_tracker_configuration_t` 结构。 允许用户指定非默认的 GPU 设备来运行人体跟踪算法。
* [Bug 修复/中断性变更] 修正了关节名称的拼写错误。 将关节名称从 `K4ABT_JOINT_SPINE_NAVAL` 更改为 `K4ABT_JOINT_SPINE_NAVEL`。

### <a name="v094"></a>v0.9.4
* [功能] 添加手关节支持。 SDK 将为每只手提供三个附加关节的信息：手、手指尖、拇指。
* [功能] 为每个检测到的关节添加预测置信度。
* [功能] 添加 CPU 模式支持。 通过更改 `k4abt_tracker_configuration_t` 中的 `cpu_only_mode` 值，现在 SDK 可以在 CPU 模式下运行，这不需要用户具有功能强大的图形卡。

### <a name="v093"></a>v0.9.3
* [功能] 发布新的 DNN 模型 dnn_model_2_0.onnx，该模型大大提高了人体跟踪的可靠性。
* [功能] 默认情况下禁用时间平滑。 跟踪的关节会响应更迅速。
* [功能] 提高了人体索引映射的准确性。
* [Bug 修复] 修复了传感器方向设置无效的 bug。
* [Bug 修复] 将 body_index_map 类型从 K4A_IMAGE_FORMAT_CUSTOM 更改为 K4A_IMAGE_FORMAT_CUSTOM8。
* [已知问题] 两个相近的身体可能合并成单个实体部分。

### <a name="v092"></a>v0.9.2
* [中断性变更] 更新为依赖最新的 Azure Kinect 传感器 SDK 1.2.0。
* [API 更改] `k4abt_tracker_create` 函数将开始接受 `k4abt_tracker_configuration_t` 输入。 
* [API 更改] 将 `k4abt_frame_get_timestamp_usec` API 更改为 `k4abt_frame_get_device_timestamp_usec`，使其更具体且与传感器 SDK 1.2.0 保持一致。
* [功能] 允许用户在创建跟踪器时指定传感器安装方向，以便在以不同角度安装时获得更准确的人体跟踪结果。
* [功能] 提供了新的 API `k4abt_tracker_set_temporal_smoothing` 以更改用户想要应用的时间平滑量。
* [功能] 添加了 C++ 包装器 k4abt.hpp。
* [功能] 添加了版本定义头文件 k4abtversion.h。
* [Bug 修复] 修复了导致 CPU 使用率极高的 bug。
* [Bug 修复] 修复了记录器崩溃 bug。

### <a name="v091"></a>v0.9.1
* [Bug 修复] 修复了销毁跟踪器时的内存泄漏
* [Bug 修复] 改进了缺少依赖项的错误消息
* [Bug 修复] 创建第二个跟踪器实例时失败而不崩溃
* [Bug 修复] 记录器环境变量现在可正常工作
* Linux 支持

### <a name="v090"></a>v0.9.0

* [中断性变更] 将 SDK 依赖项降级到 CUDA 10.0（从 CUDA 10.1）。 ONNX 运行时最高仅正式支持 CUDA 10.0 版本。
* [中断性变更] 已切换到 ONNX 运行时，弃用了 Tensorflow 运行时。 这可以减少第一帧的启动时间和内存用量。 此外还可以减小 SDK 二进制大小。
* [API 更改] 已将 `k4abt_tracker_queue_capture()` 重命名为 `k4abt_tracker_enqueue_capture()`
* [API 更改] 已将 `k4abt_frame_get_body()` 划分为两个单独的函数：`k4abt_frame_get_body_skeleton()` 和 `k4abt_frame_get_body_id()`。 现在，无需复制整个主干结构即可查询人体 ID。
* [API 更改] 添加了 `k4abt_frame_get_timestamp_usec()` 函数用于简化用户查询人体帧时间戳的步骤。
* 进一步改进了人体跟踪算法的准确性和跟踪可靠性

### <a name="v030"></a>v0.3.0

* [中断性变更] 通过转移到 CUDA 10.1 依赖项添加了对 NVIDIA RTX GPU 的支持
* [API 更改] 提供与 Kinect for Windows v2 SDK 一致的关节输出（以毫米为单位）
* 进一步改进了人体跟踪算法的准确性和可靠性。

## <a name="next-steps"></a>后续步骤

- [Azure Kinect DK 概述](about-azure-kinect-dk.md)

- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [设置 Azure Kinect 人体跟踪](body-sdk-setup.md)
