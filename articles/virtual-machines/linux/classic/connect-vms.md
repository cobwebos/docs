---
title: "连接云服务中的 Linux VM | Microsoft Docs"
description: "将使用经典部署模型创建的 Linux 虚拟机连接到 Azure 云服务或虚拟网络。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: e222645509640b104410f87e4bcd22834c8d9ec1
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017


---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>将使用经典部署模型创建的 Linux 虚拟机连接到虚拟网络或云服务
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。

使用经典部署模型创建的 Linux 虚拟机一律放置在云服务中。 云服务充当容器，并提供唯一的公用 DNS 名称、公用 IP 地址，以及一组通过 Internet 访问虚拟机的终结点。 云服务可以位于虚拟网络中，但这不是必要条件。 也可以[将 Windows 虚拟机连接到虚拟网络或云服务](../../windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

如果云服务不在虚拟网络中，就称为*独立*云服务。 独立云服务中的虚拟机使用其他虚拟机的公用 DNS 名称与其通信，流量通过 Internet 传送。 如果云服务是在虚拟网络中，则该云服务中的虚拟机可与虚拟网络中的其他所有虚拟机通信，而不需要通过 Internet 传送任何流量。

如果你将虚拟机放在相同的独立云服务中，你仍然可以使用负载均衡和可用性集。 有关详细信息，请参阅[对虚拟机进行负载均衡](../../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[管理虚拟机的可用性](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 不过，你无法组织子网上的虚拟机，也无法将独立云服务连接到本地网络。 下面是一个示例：

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>后续步骤
创建虚拟机后，建议[添加数据磁盘](attach-disk.md)，以便服务和工作负荷有地方存储数据。

