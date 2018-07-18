---
title: 在 Azure 开发测试实验室中配置虚拟网络 | Microsoft Docs
description: 了解如何配置现有虚拟网络和子网并通过 Azure 开发测试实验室在 VM 中使用它们
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 0141ea8a88c0322e6f56cbea56d3a43c923769af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687782"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置虚拟网络
如文章[将 VM 添加到实验室](devtest-lab-add-vm.md)中所述，在实验室中创建 VM 时，可指定已配置的虚拟网络。 例如，可能需要使用 ExpressRoute 或站点到站点 VPN 配置的虚拟网络从 VM 访问公司网络资源。

本文说明如何将现有虚拟网络添加到实验室的虚拟网络设置中，以便在创建 VM 时可进行选择。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>使用 Azure 门户为实验室配置虚拟网络
以下步骤介绍如何将现有虚拟网络（以及子网）添加到实验室，以便在同一实验室创建 VM 时使用它。 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表，选择所需的实验室。 
1. 在实验室的主窗格中，选择“配置和策略”。

    ![访问实验室的配置和策略](./media/devtest-lab-configure-vnet/policies-menu.png)
1. 在“外部资源”部分，选择“虚拟网络”。 将显示为当前实验室配置的虚拟网络列表以及为实验室创建的默认虚拟网络。 
1. 选择“+ 添加”。
   
    ![将现有虚拟网络添加到实验室](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. 在“虚拟网络”窗格中，选择“[选择虚拟网络]”。
   
    ![选择现有虚拟网络](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. 在“选择虚拟网络”窗格中，选择所需虚拟网络。 将显示一个列表，其中显示订阅中所有与实验室在同一区域的虚拟网络。
1. 选择虚拟网络后，将返回到“虚拟网络”窗格。 选择底部列表中的子网。

    ![子网列表](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    此时会显示“实验室子网”窗格。

    ![“实验室子网”窗格](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - 指定**实验室子网名称**。
   - 若要允许在实验室 VM 创建中使用子网，请选择“在虚拟机创建中使用”。
   - 若要启用 [共享公共 IP 地址](devtest-lab-shared-ip.md)，请选择“启用共享公共 IP”。
   - 若要在子网中允许公共 IP 地址，请选择“允许公共 IP 创建”。
   - 在“每个用户的最大虚拟机数量”字段中，指定每个子网中每个用户的最大 VM 数量。 如果不希望限制的 VM 的数目，请将此字段留空。
1. 选择“确定”关闭“实验室子网”窗格。
1. 选择“保存”关闭“虚拟网络”窗格。

既然已配置虚拟网络，则可在创建 VM 时选择它。 要了解如何创建 VM 和指定虚拟网络，请参阅文章[将 VM 添加到实验室](devtest-lab-add-vm.md)。 

Azure 的[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network)提供有关如何使用 VNet 的详细信息，包括如何设置和管理 VNet 以及如何将其连接到本地网络。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
将所需虚拟网络添加到实验室后，下一步为[将 VM 添加到实验室](devtest-lab-add-vm.md)。

