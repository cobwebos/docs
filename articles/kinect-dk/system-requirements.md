---
title: Azure Kinect 传感器 SDK 系统要求
description: 传感器 SDK 系统要求
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azure, kinect, 系统要求, CPU, GPU, USB, 设置, 安装, 最低, 要求
ms.openlocfilehash: c43d13280dfad566809a3711ce1b7746f53e9146
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679184"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect 传感器 SDK 系统要求

本文档详细说明安装传感器 SDK 以及成功部署 Azure Kinect DK 所要满足的系统要求。

## <a name="supported-operating-systems-and-architectures"></a>支持的操作系统和体系结构

- Windows 10 2018 年 4 月版 (x64) 或更高版本
- 装有 OpenGLv4.4 或更高版本 GPU 驱动程序的 Linux Ubuntu 18.04 (x64)

传感器 SDK 适用于本机 C/C++ Windows 应用程序的 Windows API (Win32)。 该 SDK 目前不适用于 UWP 应用程序。 S 模式的 Windows 10 不支持 Azure Kinect DK。

## <a name="development-environment-requirements"></a>开发环境要求

若要为传感器 SDK 开发供稿，请访问 [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)。

## <a name="minimum-host-pc-hardware-requirements"></a>主机电脑的最低硬件要求

电脑主机的硬件要求取决于在主机电脑上执行的应用程序/算法/传感器帧速率/分辨率。 对 Windows 建议的最低传感器 SDK 配置为：

- 第七代 Intel® CoreTM i3 处理器（双核 2.4 GHz，搭载 HD620 或更快的 GPU）
- 4 GB 内存
- 专用 USB3 端口
- 支持 OpenGL 4.4 或 DirectX 11.0 的图形驱动程序

根据具体的用例，低端或早期的 CPU 可能也适用。

此外，性能根据所用的 Windows/Linux 操作系统和图形驱动程序而异。

## <a name="body-tracking-host-pc-hardware-requirements"></a>人体跟踪主机电脑的硬件要求

人体跟踪电脑主机的要求比一般电脑主机的要求更高。 对 Windows 建议的最低人体跟踪 SDK 配置为：

- 第七代 Intel® CoreTM i5 处理器（四核 2.4 GHz 或更快）
- 4 GB 内存
- NVIDIA GEFORCE GTX 1070 或更佳
- 专用 USB3 端口

根据具体的用例，低端或早期的 CPU 和 NVIDIA GPU 可能也适用。

## <a name="usb3"></a>USB3

USB 主控制器存在一些已知的兼容性问题。 可以在[故障排除页](troubleshooting.md#usb3-host-controller-compatibility)上找到详细信息

## <a name="next-steps"></a>后续步骤

- [Azure Kinect DK 概述](about-azure-kinect-dk.md)

- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [设置 Azure Kinect 人体跟踪](body-sdk-setup.md)
