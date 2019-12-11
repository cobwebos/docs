---
title: 在 Azure 开发测试实验室虚拟机上启用浏览器连接 |Microsoft Docs
description: 开发测试实验室现已与 Azure 堡垒集成，作为实验室的所有者，你可以通过浏览器启用访问所有实验室虚拟机。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970751"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>启用实验室虚拟机上的浏览器连接 
开发测试 Labs 与[Azure 堡垒](https://docs.microsoft.com/azure/bastion/)集成，使你能够通过浏览器连接到你的虚拟机。 首先需要在实验室虚拟机上启用浏览器连接。

作为实验室的所有者，你可以通过浏览器启用访问所有实验室虚拟机。 无需额外的客户端、代理或软件。 Azure Bastion 可通过 SSL 直接在 Azure 门户中实现与虚拟机之间的安全、无缝 RDP/SSH 连接。 通过 Azure 堡垒连接时，虚拟机不需要公共 IP 地址。 有关详细信息，请参阅[什么是 Azure 堡垒？](../bastion/bastion-overview.md)


本文介绍如何在实验室虚拟机上启用浏览器连接。

## <a name="prerequisites"></a>必备组件 
在现有实验室的虚拟网络中部署堡垒主机 **（或）** 将实验室连接到使用堡垒配置的虚拟网络。 

若要了解如何在虚拟网络中部署堡垒主机，请参阅[创建 Azure 堡垒主机](../bastion/bastion-create-host-portal.md)。 创建堡垒主机时，选择实验室的虚拟网络。 

首先，需要在堡垒虚拟网络中创建第二个子网，因为 AzureBastionSubnet 不允许在其中创建非堡垒资源。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>在堡垒虚拟网络中创建第二个子网
无法在 Azure 堡垒子网中创建实验室 Vm。 在堡垒虚拟网络中创建另一个子网，如下图所示：

![Azure 堡垒虚拟网络中的第二个子网](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>在子网中启用 VM 创建
现在，通过执行以下步骤，在此子网中启用 Vm 的创建： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择左侧导航菜单中的 "**所有服务**"。 
1. 从列表中选择 "**开发测试实验室**"。 
1. 从实验室列表中，选择*实验室*。 

    > [!NOTE]
    > Azure 堡垒现已在以下地区正式发布：美国西部、美国东部、西欧、美国中南部、澳大利亚东部和日本东部。 如果你的实验室不在其中一个区域中，请在其中一个区域中创建实验室。 
    
1. 在左侧菜单的 "**设置**" 部分中选择 "**配置和策略**"。 
1. 选择 "**虚拟网络**"。
1. 从工具栏中选择 "**添加**"。 
1. 选择已部署堡垒主机的**虚拟网络**。 
1. 选择 Vm 的子网，而不是之前创建的 Vm 的**AzureBastionSubnet**。 如果底部的列表中看不到子网，请关闭该页并重新打开它。 

    ![在子网中启用 VM 创建](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. 选择 **"在虚拟机创建中使用"** 选项。 
1. 在工具栏上选择“保存”。 
1. 如果有用于实验室的旧虚拟网络，请选择 "* *...* " 和**删除**。 

## <a name="enable-browser-connection"></a>启用浏览器连接 

在实验室中有一个堡垒配置的虚拟网络作为实验室所有者后，可以在实验室虚拟机上启用浏览器连接。

若要在实验室虚拟机上启用浏览器连接，请执行以下步骤：

1. 在 Azure 门户中，导航到*实验室*。
1. 选择“配置和策略”。
1. 在 "**设置**" 中，选择 "**浏览器连接**"。 如果看不到此选项，请关闭 "**配置策略**" 页，然后重新打开它。 

    ![启用浏览器连接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何使用浏览器连接到你的 Vm：[通过浏览器连接到你的虚拟机](connect-virtual-machine-through-browser.md)
