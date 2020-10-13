---
title: 使用 Azure 门户托管磁盘上的加密来启用端到端加密
description: 使用主机上的加密在 Azure 托管磁盘上启用端到端加密-Azure 门户。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4498e78b408f64ab5bc00b9f8730559b90c95d57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817387"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>使用 Azure 门户在主机上使用加密启用端到端加密

启用主机加密时，存储在 VM 主机上的数据将静态加密，且已加密的数据将流向存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅：

* Linux： [VM 数据的主机端对端加密](./linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

* Windows： [VM 数据的主机端对端加密](./windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>先决条件

为了能够为 VM 或虚拟机规模集使用主机加密，必须在订阅上启用该功能。 向 encryptionAtHost@microsoft 发送电子邮件。 带有订阅 Id 的 com 用于为你的订阅启用该功能。

使用 [提供的链接](https://aka.ms/diskencryptionupdates)登录到 Azure 门户。

> [!IMPORTANT]
> 必须使用提供的 [链接](https://aka.ms/diskencryptionupdates) 访问 Azure 门户。 主机上的加密当前在公共 Azure 门户中不可见，无需使用链接。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>创建 Azure Key Vault 和磁盘加密集

启用该功能后，需要设置 Azure Key Vault 和磁盘加密集（如果尚未这样做）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

你必须部署新的 VM 以便在主机上启用加密，而不能在现有 Vm 上启用它。

1. 搜索“虚拟机”，然后选择“+ 添加”以创建 VM 。
1. 创建新的虚拟机，选择适当的区域和受支持的 VM 大小。
1. 根据需要在 " **基本** " 边栏选项卡中填写其他值，然后转到 " **磁盘** " 边栏选项卡。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="将突出显示 &quot;虚拟机创建基本信息&quot; 边栏选项卡、&quot;区域&quot; 和 &quot;V&quot; 大小的屏幕截图。&quot;:::

1. 在 &quot; **磁盘** " 边栏选项卡上，选择 **"是"** 以 **在主机上加密**。
1. 根据需要进行剩余选择。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="将突出显示 &quot;虚拟机创建基本信息&quot; 边栏选项卡、&quot;区域&quot; 和 &quot;V&quot; 大小的屏幕截图。&quot;:::

1. 在 &quot; **磁盘** ":::

1. 完成 VM 部署过程，选择适合你的环境的选项。

你现在已在主机上部署了启用了加密的 VM，则会在主机上使用加密对其所有关联的磁盘进行加密。

## <a name="next-steps"></a>后续步骤

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)