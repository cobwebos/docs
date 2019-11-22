---
title: 快速入门 - 设置 Azure Kinect 人体跟踪
description: 此快速入门将为 Azure Kinect 设置人体跟踪 SDK。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 传感器, 访问, 深度, sdk, 人体, 跟踪, 关节, 设置, cuda, nvidia
ms.openlocfilehash: 21655bb5a7e5b3eeef008f715e703512734f3960
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931765"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>快速入门：设置 Azure Kinect 人体跟踪

本快速入门将引导你完成在 Azure Kinect DK 上运行人体跟踪的过程。

## <a name="system-requirements"></a>系统要求

人体跟踪 SDK 要求在主机电脑中安装 NVIDIA GPU。 [系统要求](system-requirements.md)页中描述了建议的人体跟踪主机电脑要求。

## <a name="install-software"></a>安装软件

### <a name="install-the-latest-nvidia-driverhttpswwwnvidiacomdownloadindexaspxlangen-us"></a>[安装最新的 NVIDIA 驱动程序](https://www.nvidia.com/Download/index.aspx?lang=en-us)

请下载并安装显卡的最新 NVIDIA 驱动程序。 旧版驱动程序可能与随人体跟踪 SDK 一起重新分发的 CUDA 二进制文件不兼容。

### <a name="visual-c-redistributable-for-visual-studio-2015httpswwwmicrosoftcomen-usdownloaddetailsaspxid48145"></a>[Visual C++ Redistributable for Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

下载并安装 Microsoft Visual C++ Redistributable for Visual Studio 2015。 

## <a name="set-up-hardware"></a>设置硬件

### <a name="set-up-azure-kinect-dkset-up-azure-kinect-dkmd"></a>[设置 Azure Kinect DK](set-up-azure-kinect-dk.md)

启动 [Azure Kinect 查看器](azure-kinect-viewer.md)来检查是否已正确设置 Azure Kinect DK。

## <a name="download-the-body-tracking-sdk"></a>下载人体跟踪 SDK
 
1. 选择[下载人体跟踪 SDK](body-sdk-download.md) 的链接
2. 在电脑上安装人体跟踪 SDK。

## <a name="verify-body-tracking"></a>验证人体跟踪

启动 **Azure Kinect 人体跟踪查看器**来检查是否已正确设置人体跟踪 SDK。 该查看器是使用 SDK msi 安装程序安装的。 可以在开始菜单或 `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` 中找到它。

如果没有足够强大的 GPU 但仍想测试结果，可以通过以下命令在命令行中启动 **Azure Kinect 人体跟踪查看器**：`<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

如果设置全都正确，应会显示一个窗口，其中包含 3D 点云和跟踪到的人体。


![人体跟踪 3D 查看器](./media/quickstarts/samples-simple3dviewer.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[生成第一个人体跟踪应用程序](build-first-body-app.md)

