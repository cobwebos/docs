---
title: "连接云服务中的 Linux Vm |Microsoft 文档"
description: "使用经典部署模型到 Azure 云服务或虚拟网络创建的 Linux 虚拟机连接。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 2fd23055-6b34-4ef0-88a8-fc19e32fb3c9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: e222645509640b104410f87e4bcd22834c8d9ec1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>连接使用虚拟网络或云服务使用经典部署模型创建的 Linux 虚拟机
> [!IMPORTANT]
> Azure 具有用于创建和使用资源的两个不同的部署模型：[资源管理器和经典](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

使用经典部署模型创建的 Linux 虚拟机始终放置在云服务。 云服务充当容器，并提供唯一的公共 DNS 名称、 公共 IP 地址和一组终结点以通过 Internet 访问虚拟机。 云服务可以在虚拟网络中，但这不是一项要求。 你还可以[将 Windows 虚拟机连接到虚拟网络或云服务](../../windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

如果云服务不在虚拟网络中，则将调用*独立*云服务。 独立云服务中的虚拟机通信与其他虚拟机使用其他虚拟机的公共 DNS 名称，并通过 Internet 传送流量。 如果云服务是虚拟网络中，该云服务中的虚拟机能够与虚拟网络中的所有其他虚拟机而无需通过 Internet 发送的任何流量。

如果将虚拟机放在相同的独立云服务中，你仍然可以使用负载平衡和可用性集。 有关详细信息，请参阅[虚拟机进行负载平衡](../../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[管理虚拟机的可用性](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 但是，你无法组织子网上的虚拟机或连接到你的本地网络的独立云服务。 下面是一个示例：

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>后续步骤
创建虚拟机后，它是一个好主意[添加数据磁盘](attach-disk.md)让你的服务和工作负荷存储数据的位置。
