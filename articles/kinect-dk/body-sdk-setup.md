---
title: 快速入门 - 设置 Azure Kinect 人体跟踪
description: 本快速入门提供设置 Azure Kinect 人体跟踪 SDK 的步骤
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 传感器, 访问, 深度, sdk, 人体, 跟踪, 关节, 设置, cuda, nvidia
ms.openlocfilehash: 6d610e62a7c9df33559f7f2cedceec27651e1cca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451418"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>快速入门：设置 Azure Kinect 人体跟踪

本快速入门将引导你完成在 Azure Kinect DK 上运行人体跟踪的过程。

## <a name="system-requirements"></a>系统要求

人体跟踪 SDK 要求在主机电脑中安装 NVIDIA GPU。 [系统要求](system-requirements.md)页中描述了建议的人体跟踪主机电脑要求。

## <a name="install-software"></a>安装软件

### <a name="cuda-100httpsdevelopernvidiacomcuda-100-download-archive"></a>[CUDA 10.0](https://developer.nvidia.com/cuda-10.0-download-archive)

遵照屏幕上的提示安装 CUDA 10.0 和所有修补程序。

>[!NOTE]
> 如果使用“快速”选项安装失败，请选择“自定义”安装选项并单击“下一步”。
> 然后展开“CUDA”标记，并取消选择“Visual Studio 集成”。

![CUDA 安装图像 1](./media/quickstarts/install-cuda1.png)![CUDA 安装图像 2](./media/quickstarts/install-cuda2.png)

### <a name="cudnn-v75x-for-cuda-100httpsdevelopernvidiacomrdpcudnn-archive"></a>[cuDNN v7.5.x for CUDA 10.0](https://developer.nvidia.com/rdp/cudnn-archive)

使用开发人员计划成员身份登录到 NVIDIA 开发人员网站，并下载 `cudnn64_7.dll`。 确保将 DLL 路径添加到“环境变量 - 路径”：
1. 启动“控制面板”-> 选择“系统和安全性”->“系统”->“高级系统设置”

    ![设置系统路径 1](./media/quickstarts/install-system-path1.png)

2. 选择“环境变量”-> 双击“系统变量”块下的“路径”变量 -> 确保存在包含 `cudnn64_7.dll` 的路径。

    ![设置系统路径 2](./media/quickstarts/install-system-path2.png)

### <a name="install-the-latest-nvidia-driverhttpswwwnvidiacomdownloadindexaspxlangen-us"></a>[安装最新的 NVIDIA 驱动程序](https://www.nvidia.com/Download/index.aspx?lang=en-us)

CUDA 10.0 安装旧版的 NVIDIA 图形驱动程序。 请下载并安装显卡的最新 NVIDIA 驱动程序。

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

如果设置全都正确，应会显示一个窗口，其中包含 3D 点云和跟踪到的人体。

![人体跟踪 3D 查看器](./media/quickstarts/samples-simple3dviewer.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[生成第一个人体跟踪应用程序](build-first-body-app.md)

