---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177001"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

创建并设置好 Key Vault 和磁盘加密集之后，接下来即可使用加密来部署 VM。
VM 部署过程与标准部署过程类似，唯一的差别在于，你需要将 VM 部署到与其他资源相同的区域中，并选择使用客户托管密钥。

1. 搜索“虚拟机”，然后选择“+ 添加”以创建 VM 。
1. 在 "**基本**" 边栏选项卡中，选择与磁盘加密集相同的区域，并 Azure Key Vault。
1. 根据需要，在 "**基本**" 边栏选项卡中填写其他值。

    ![VM 创建体验的屏幕截图，其中突出显示了区域值。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. 在 "**磁盘**" 边栏选项卡上，选择 "**静态加密"，并使用客户管理的密钥**。
1. 在“磁盘加密集”下拉列表中选择磁盘加密集。
1. 根据需要进行剩余选择。

    ![VM 创建体验的屏幕截图，“磁盘”边栏选项卡。 其中突出显示了“磁盘加密集”下拉列表。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>在现有磁盘上启用

> [!CAUTION]
> 在附加到 VM 的任何磁盘上启用磁盘加密将需要你停止 VM。
    
1. 导航到与磁盘加密集位于同一区域中的 VM。
1. 打开 VM 并选择“停止”。

    ![示例 VM 的主覆盖屏幕截图，其中突出显示了 "停止" 按钮。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM 停止后，选择“磁盘”，然后选择要加密的磁盘。

    ![示例 VM 的屏幕截图，其中“磁盘”边栏选项卡处于打开状态。 OS 磁盘突出显示，作为示例磁盘供你选择。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. 依次选择“加密”、“使用客户托管密钥进行静态加密”，然后在下拉列表中选择“磁盘加密集” 。
1. 选择“保存” 。

    ![示例 OS 磁盘的屏幕截图。 “加密”边栏选项卡处于打开状态，“使用客户托管密钥进行静态加密”处于选中状态以及示例 Azure Key Vault。 完成这些选择后，“保存”按钮处于选中状态。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 对于附加到你想要加密的 VM 的任何其他磁盘，请重复此过程。
1. 当磁盘完成切换到客户托管密钥后，如果没有其他需要进行加密的附加磁盘，则可以启动 VM。
