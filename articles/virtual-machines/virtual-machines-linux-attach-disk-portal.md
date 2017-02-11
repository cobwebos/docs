---
title: "将数据磁盘附加到 Linux VM | Microsoft Docs"
description: "如何使用 Resource Manager 部署模型在 Azure 门户中将新磁盘或现有数据磁盘附加到 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何在 Azure 门户中将数据磁盘附加到 Linux VM
本文介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到 Linux 虚拟机。 也可以[在 Azure 门户中将数据磁盘附加到 Windows VM](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 在开始之前，请查看以下提示：

* 虚拟机的大小决定了可以附加多少个磁盘。 有关详细信息，请参阅[虚拟机大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 要使用高级存储，需要使用 DS 系列或 GS 序列虚拟机。 可以从高级存储帐户和标准存储帐户通过这些虚拟机使用磁盘。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../storage/storage-premium-storage.md)。
* 附加到虚拟机的磁盘实际上是 Azure 存储帐户中的 .vhd 文件。 有关详细信息，请参阅[关于虚拟机的磁盘和 VHD](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 对于新磁盘，你不必首先创建它，因为 Azure 将在附加磁盘时创建该磁盘。
* 对于现有磁盘，Azure 存储帐户中必须要有可用的 .vhd 文件。 你可以使用已经存在的 .vhd（如果该磁盘没有附加到另一虚拟机），或者将你自己的 .vhd 文件上载到存储帐户。

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>后续步骤
添加磁盘后，需要对它进行准备以供使用。 在[此文](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux)中了解虚拟机的操作系统：“如何：在 Linux 中初始化新的数据磁盘”。



<!--HONumber=Nov16_HO3-->


