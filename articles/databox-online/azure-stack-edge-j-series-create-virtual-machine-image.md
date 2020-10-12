---
title: 为 Azure Stack Edge Pro GPU 设备创建 VM 映像
description: 描述如何创建用于 Azure Stack Edge Pro GPU 设备的 linux 或 Windows VM 映像。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 745d0df07b6d0d01acf0d564df8c242e16f3f56d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890992"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>为 Azure Stack Edge Pro 设备创建自定义 VM 映像

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

若要在 Azure Stack Edge Pro 设备上部署 VM，你需要能够创建可用于创建 VM 的自定义 VM 映像。 本文介绍了创建 Linux 或 Windows VM 自定义映像所需的步骤，可使用这些自定义映像在 Azure Stack Edge Pro 设备上部署 VM。

## <a name="vm-image-workflow"></a>VM 映像工作流

此工作流要求你在 Azure 中创建虚拟机、自定义 VM、实现通用化，然后下载与该 VM 相对应的 VHD。 系统会将此通用化后的 VHD 上传到 Azure Stack Edge Pro，基于该 VHD 创建托管磁盘，再基于托管磁盘创建映像，最后基于该映像创建 VM。   

有关详细信息，请转到[使用 Azure PowerShell 在 Azure Stack Edge Pro 设备上部署 VM](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。


## <a name="create-a-windows-custom-vm-image"></a>创建 Windows 自定义 VM 映像

执行以下步骤以创建 Windows VM 映像。

1. 创建 Windows 虚拟机。 有关详细信息，请转到[教程：使用 Azure PowerShell 创建和管理 Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

2. 下载现有 OS 磁盘。

    - 按照[下载 VHD](../virtual-machines/windows/download-vhd.md) 中的步骤操作。

    - 使用以下 `sysprep` 命令，而不是上述过程中描述的内容。
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       你还可以参阅 [Sysprep（系统准备）概述](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

现在使用此 VHD 在 Azure Stack Edge Pro 设备上创建和部署 VM。

## <a name="create-a-linux-custom-vm-image"></a>创建 Linux 自定义 VM 映像

执行以下步骤以创建 Linux VM 映像。

1. 创建 Linux 虚拟机。 有关详细信息，请转到[教程：使用 Azure CLI 创建和管理 Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)。

1. 取消预配 VM。 使用 Azure VM 代理删除计算机特定文件和数据。 在源 Linux VM 上，使用带 `-deprovision+user` 参数的 `waagent` 命令。 有关详细信息，请参阅[了解和使用 Azure Linux 代理](../virtual-machines/extensions/agent-linux.md)。

    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入以下命令：
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > 仅在将捕获为映像的 VM 上运行此命令。 此命令无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。 `+user` 参数还会删除上次预配的用户帐户。 要保留 VM 中的用户帐户凭据，请仅使用 `-deprovision`。
     
    3. 按 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
    4. 该命令完成后，请输入“退出”以关闭 SSH 客户端。  VM 在此时仍会运行。


1. [下载现有 OS 磁盘](../virtual-machines/linux/download-vhd.md)。

现在使用此 VHD 在 Azure Stack Edge Pro 设备上创建和部署 VM。 可以使用以下两个 Azure 市场映像创建 Linux 自定义映像：

|项名称  |说明  |发布者  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server 是全球流行的 Linux 云环境。|Canonical|
|[Debian 8“Jessie”](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux 是最流行的 Linux 分发版之一。     |credativ|

有关有效（目前未测试）Azure 市场映像的完整列表，请参阅[可用于 Azure Stack Hub 的 Azure 市场项](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910)。


## <a name="next-steps"></a>后续步骤

[在 Azure Stack Edge Pro 设备上部署 VM](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)。
