---
title: Azure Kinect 人体跟踪 SDK 下载
description: 人体跟踪 SDK 的下载链接
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, 下载更新, 最新, 可用, 安装, 人体, 跟踪
ms.openlocfilehash: 4374a812fa83beef5fd0ab46a4a2da8228cc6d28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453120"
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
0.9.0 | [msi](https://download.microsoft.com/download/F/D/E/FDE380A1-6E63-4571-94F3-F3744E9EE85A/Azure%20Kinect%20Body%20Tracking%20SDK%200.9.0.msi)

> [!NOTE]
> 安装 SDK 时，请记住要安装到的路径。 例如，“C:\Program Files\Azure Kinect Body Tracking SDK 0.9.0”。 你将要在此路径中查找文章中参考的示例。

## <a name="change-log"></a>更改日志

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