---
title: "捕获 Azure Windows VM 的映像 | Microsoft Docs"
description: "捕获使用经典部署模型创建的 Azure Windows 虚拟机的映像。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 6032263848c469ce2f416306e5c91c29f4cb30e4
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>捕获使用经典部署模型创建的 Azure Windows 虚拟机的映像。
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 有关资源管理器模型信息，请参阅[在 Azure 中捕获通用 VM 的托管映像](../capture-image-resource.md)。

本文将演示如何捕获运行 Windows 的 Azure 虚拟机，可以将它用作映像来创建其他虚拟机。 此映像包含操作系统磁盘和任何附加到虚拟机的数据磁盘。 由于其不包括网络配置，因此创建使用此映像的其他虚拟机时，需设置网络配置。

Azure 将映像存储在“VM 映像(经典)”下，这是查看所有 Azure 服务时列出的**计算**服务。 上传的任何映像都会存储在同一位置。 有关映像的详细信息，请参阅[关于虚拟机的映像](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json)。

## <a name="before-you-begin"></a>开始之前
这些步骤假定已创建了 Azure 虚拟机并配置了操作系统，包括附加任何数据磁盘。 如果尚未执行此操作，请参阅以下文章以了解如何创建和准备虚拟机：

* [从映像创建虚拟机](createportal.md)
* [如何将数据磁盘附加到虚拟机](attach-disk.md)
* 确保 Sysprep 支持服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [!WARNING]
> 此过程会在捕获原始虚拟机后将其删除。
>
>

捕获 Azure 虚拟机映像之前，建议备份目标虚拟机。 可以使用 Azure 备份来备份 Azure 虚拟机。 有关详细信息，请参阅[备份 Azure 虚拟机](../../../backup/backup-azure-vms.md)。 认证合作伙伴提供其他解决方案。 若要了解当前提供的内容，请搜索 Azure 应用商店。

## <a name="capture-the-virtual-machine"></a>捕获虚拟机
1. 在 [Azure 门户](http://portal.azure.com)中，**连接**到虚拟机。 有关说明，请参阅[如何登录到运行 Windows Server 的虚拟机][How to sign in to a virtual machine running Windows Server]。
2. 以管理员身份打开“命令提示符”窗口。
3. 将目录更改为 `%windir%\system32\sysprep`，然后运行 sysprep.exe。
4. 此时会显示“系统准备工具”对话框。 请执行以下操作：

   * 在“系统清理操作”中，选择“进入系统全新体验(OOBE)”，并确保选中“通用化”。 有关使用 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介][How to Use Sysprep: An Introduction]。
   * 在“关机选项”中选择“关机”。
   * 单击 **“确定”**。

   ![运行 Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep 将关闭虚拟机，这会在 Azure 门户中将虚拟机的状态更改为“已停止”。
6. 在 Azure 门户中，单击“虚拟机(经典)”，然后选择要捕获的虚拟机。 查看“更多服务”时，“VM 映像(经典)”组在“计算”下列出。

7. 在命令栏中，单击“捕获”。

   ![捕获虚拟机](./media/capture-image/CaptureVM.png)

   此时将显示“捕获虚拟机”对话框。

8. 在“映像名称”中，键入新映像的名称。 在“映像标签”中，键入新映像的标签。

9. 单击“我已在虚拟机上运行 Sysprep”。 此复选框是指步骤 3-5 中的 Sysprep 操作。 将 Windows Server 映像添加到自定义映像集之前，_必须_通过运行 Sysprep 使该映像通用化。

10. 捕获完成后，可在**应用商店**的“计算”、“VM 映像(经典)”容器中获取新映像。

    ![成功捕获映像](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>后续步骤
该映像已就绪，可用于创建虚拟机了。 为此，通过在服务菜单底部选择“更多服务”菜单项，然后在“计算”组中选择“VM 映像(经典)”来创建虚拟机。 有关说明，请参阅[从映像创建虚拟机](createportal.md)。

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

