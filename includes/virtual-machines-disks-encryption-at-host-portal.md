---
title: 包含文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171357"
---
## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>必备条件

为了能够在 Vm 或虚拟机规模集的主机上使用加密，必须在订阅上启用该功能。 向 encryptionAtHost@microsoft 发送电子邮件。 带有订阅 Id 的 com 用于为你的订阅启用该功能。

使用[提供的链接](https://aka.ms/diskencryptionupdates)登录到 Azure 门户。

> [!IMPORTANT]
> 必须使用提供的[链接](https://aka.ms/diskencryptionupdates)访问 Azure 门户。 主机上的加密当前在公共 Azure 门户中不可见，无需使用链接。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>创建 Azure Key Vault 和磁盘加密集

启用该功能后，需要设置 Azure Key Vault 和磁盘加密集（如果尚未这样做）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

你必须部署新的 VM 以便在主机上启用加密，而不能在现有 Vm 上启用它。

1. 搜索“虚拟机”，然后选择“+ 添加”以创建 VM 。
1. 创建新的虚拟机，选择适当的区域和受支持的 VM 大小。
1. 根据需要在 "**基本**" 边栏选项卡中填写其他值，然后转到 "**磁盘**" 边栏选项卡。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="将突出显示 "虚拟机创建基本信息" 边栏选项卡、"区域" 和 "V" 大小的屏幕截图。":::

1. 在 "**磁盘**" 边栏选项卡上，选择 **"是"** 以**在主机上加密**。
1. 根据需要进行剩余选择。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="虚拟机 "创建磁盘" 边栏选项卡的屏幕截图会突出显示 "在主机上加密"。":::

1. 完成 VM 部署过程，选择适合你的环境的选项。

你现在已在主机上部署了启用了加密的 VM，则会在主机上使用加密对其所有关联的磁盘进行加密。