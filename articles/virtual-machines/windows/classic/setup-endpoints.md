---
title: 在经典 Windows VM 上设置终结点 | Microsoft Docs
description: 了解如何在 Azure 门户中为经典 Windows VM 设置终结点，以便能够与 Azure 中的 Windows 虚拟机通信。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957159"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>使用经典部署模型在 Windows 虚拟机上设置终结点
在 Azure 中使用经典部署模型创建的 Windows 虚拟机 (VM) 可以通过专用网络通道与同一云服务或虚拟网络中的其他 VM 自动通信。 但是，Internet 上的计算机或其他虚拟网络需要终结点才能将入站网络流量定向到 VM。 

也可以在 [Linux 虚拟机](../../linux/classic/setup-endpoints.md)上设置终结点。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../../../resource-manager-deployment-model.md)。 本文介绍经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

在**资源管理器**部署模型中，终结点使用**网络安全组 (NSG)** 进行配置。 有关详细信息，请参阅[使用 Azure 门户允许对 VM 进行外部访问](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

在 Azure 门户中创建 Windows VM 时，通常会自动创建常用终结点（如用于远程桌面和 Windows PowerShell 远程处理的终结点）。 以后可以根据需要配置其他终结点。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>后续步骤
* 若要使用 Azure PowerShell cmdlet 设置 VM 终结点，请参阅 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)。
* 若要使用 Azure PowerShell cmdlet 管理终结点上的 ACL，请参阅[使用 PowerShell 管理终结点的访问控制列表 (ACL)](../../../virtual-network/virtual-networks-acl-powershell.md)。
* 如果已在 Resource Manager 部署模型中创建虚拟机，可以使用 Azure PowerShell [创建网络安全组](../../../virtual-network/tutorial-filter-network-traffic.md)，控制发往 VM 的流量。
