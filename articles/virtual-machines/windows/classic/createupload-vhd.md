---
title: "使用 Powershell 创建和上传 VM 映像 | Microsoft Docs"
description: "了解如何使用经典部署模型和 Azure Powershell 创建并上传通用化 Windows Server 映像 (VHD)。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: c2540120bcb1eca9f4ba62c7dbc0675343bf4f99
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>创建 Windows Server VHD 并将其上传到 Azure
本文说明如何上传自己的通用化 VM 映像作为虚拟硬盘 (VHD)，以便使用它来创建虚拟机。 有关 Microsoft Azure 中的磁盘和 VHD 的更多详细信息，请参阅[关于虚拟机的磁盘和 VHD](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 也可以使用 Resource Manager 模型来[上传](../upload-generalized-managed.md)虚拟机。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="prerequisites"></a>先决条件
本文假定已具备以下条件：

* **Azure 订阅** - 如果没有，可以[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。
* **[Microsoft Azure PowerShell](/powershell/azure/overview)** - 已安装 Microsoft Azure PowerShell 模块并将其配置为使用订阅。
* **.VHD file** - 存储在 .vhd 文件中并附加到虚拟机的受支持 Windows 操作系统。 检查 Sysprep 是否支持 VHD 上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

    > [!IMPORTANT]
    > Microsoft Azure 不支持 VHDX 格式。 可使用 Hyper-V 管理器或 [Convert-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx) 将磁盘转换为 VHD 格式。 有关详细信息，请参阅此[博客文章](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## <a name="step-1-prep-the-vhd"></a>步骤 1：准备 VHD
在将 VHD 上传到 Azure 之前，需要使用 Sysprep 工具使 VHD 一般化。 以此准备 VHD 作为映像使用。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。 在运行 Sysprep 之前备份 VM。

在已安装操作系统的虚拟机中，完成以下过程：

1. 登录到操作系统。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 **%windir%\system32\sysprep**，然后运行 `sysprep.exe`。

    ![打开“命令提示符”窗口](./media/createupload-vhd/sysprep_commandprompt.png)
3. 此时会显示“系统准备工具”对话框。

   ![启动 Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. 在“系统准备工具”中，选择“进入系统全新体验(OOBE)”，并确保选中“通用化”。
5. 在“关机选项”中选择“关机”。
6. 单击 **“确定”**。

## <a name="step-2-create-a-storage-account-and-a-container"></a>步骤 2：创建存储帐户和容器
需要一个 Azure 存储帐户，以便有一个可向其上传 .vhd 文件的位置。 此步骤演示了如何创建一个帐户，或如何从现有帐户获取需要的信息。 将 &lsaquo; 和 &rsaquo; 括号中的变量替换为自己的信息。

1. 登录

    ```powershell
    Add-AzureAccount
    ```

2. 设置 Azure 订阅。

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. 新建存储帐户。 存储帐户的名称应该唯一且包含 3-24 个字符。 名称可以是字母和数字的任意组合。 还需要指定一个位置，例如“美国东部”

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. 将新存储帐户设置为默认值。

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. 创建新容器。

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>步骤 3：上传 .vhd 文件
使用 [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd)上传 VHD。

在上一步骤使用的 Azure PowerShell 窗口中键入以下命令，并将 &lsaquo; 和 &rsaquo; 括号中的变量替换为自己的信息。

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>步骤 4：将映像添加到自定义映像列表
使用 [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) cmdlet 将映像添加到自定义映像列表。

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>后续步骤
现在可以使用上传的映像[来创建自定义的 VM](createportal.md)。
