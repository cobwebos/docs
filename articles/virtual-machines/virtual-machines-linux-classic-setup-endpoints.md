---
title: "在经典 Linux VM 上设置终结点 | Microsoft Docs"
description: "了解如何在 Azure 经典门户中为 Linux VM 设置终结点，以便能够与 Azure 中的 Linux 虚拟机通信。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 372fc76485720f34e7c5b85edf83a01aa5263022


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>如何在 Azure 中的 Linux 经典虚拟机上设置终结点
在 Azure 中使用经典部署模型创建的所有 Linux 虚拟机都可以通过专用网络通道与同一云服务或虚拟网络中的其他虚拟机自动通信。 但是，Internet 上的计算机或其他虚拟网络需要终结点才能定向虚拟机的入站网络流量。 本文也适用于 [Windows 虚拟机](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

在 **Resource Manager** 部署模型中，终结点是使用**网络安全组 (NSG)** 配置的。 有关详细信息，请参阅 [打开端口和终结点](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

在 Azure 经典门户中创建 Linux 虚拟机时，系统通常自动为安全外壳 (SSH) 创建一个终结点。 你可以在创建虚拟机时或之后根据需要配置其他终结点。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>后续步骤
* 也可以使用 [Azure 命令行接口](../virtual-machines-command-line-tools.md)创建 VM 终结点。 运行 **azure vm endpoint create** 命令。
* 如果在 Resource Manager 部署模型中创建虚拟机，可以在 Resource Manager 模式下使用 Azure CLI [创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-cli.md)，控制发往 VM 的流量。




<!--HONumber=Nov16_HO3-->


