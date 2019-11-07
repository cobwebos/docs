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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 080dd91b2ab6792debfae3a3ccc97b0927015de4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580136"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>启用实验室虚拟机上的浏览器连接 

开发测试 Labs 与[Azure 堡垒](https://docs.microsoft.com/azure/bastion/)集成，使你能够通过浏览器连接到你的虚拟机。 首先需要在实验室虚拟机上启用浏览器连接。

作为实验室的所有者，你可以通过浏览器启用访问所有实验室虚拟机。 无需额外的客户端、代理或软件。 Azure Bastion 可通过 SSL 直接在 Azure 门户中实现与虚拟机之间的安全、无缝 RDP/SSH 连接。 通过 Azure 堡垒连接时，虚拟机不需要公共 IP 地址。 有关详细信息，请参阅[什么是 Azure 堡垒？](../bastion/bastion-overview.md)


本文介绍如何在实验室虚拟机上启用浏览器连接。

## <a name="prerequisites"></a>先决条件 
在现有实验室的虚拟网络中部署堡垒主机 **（或）** 使用已配置的 VNet 连接到实验室。 

若要了解如何在 VNet 中部署堡垒主机，请参阅[创建 Azure 堡垒主机（预览版）](../bastion/bastion-create-host-portal.md)。 创建堡垒主机时，选择实验室的虚拟网络。 

若要了解如何将实验室连接到使用堡垒配置的 VNet，请参阅[在 Azure 开发测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md)。 选择已部署堡垒主机的 VNet，其中包含**AzureBastionSubnet** 。 以下为详细步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择左侧导航菜单中的 "**所有服务**"。 
1. 从列表中选择 "**开发测试实验室**"。 
1. 从实验室列表中，选择*实验室*。 

    > [!NOTE]
    > Azure 堡垒现已在以下地区正式发布：美国西部、美国东部、西欧、美国中南部、澳大利亚东部和日本东部。 如果你的实验室不在其中一个区域中，请在其中一个区域中创建实验室。 
    
1. 在左侧菜单的 "**设置**" 部分中选择 "**配置和策略**"。 
1. 选择 "**虚拟网络**"。
1. 从工具栏中选择 "**添加**"。 
1. 选择已部署堡垒主机的**VNet** 。 
1. 选择子网： **AzureBastionSubnet**。 

    ![子网](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. 选择 **"在虚拟机创建中使用"** 选项。 
1. 在工具栏上选择“保存”。 
1. 如果有用于实验室的旧 VNet，请选择 "* *...* " 和**删除**。 

## <a name="enable-browser-connection"></a>启用浏览器连接 

在实验室内拥有堡垒配置的 VNet 后，作为实验室所有者，你可以在实验室虚拟机上启用浏览器连接。

若要在实验室虚拟机上启用浏览器连接，请执行以下步骤：

1. 在 Azure 门户中，导航到*实验室*。
1. 选择“配置和策略”。
1. 在 "**设置**" 中，选择 "**浏览器连接（预览版）** "。

    ![启用浏览器连接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何使用浏览器连接到你的 Vm：[通过浏览器连接到你的虚拟机](connect-virtual-machine-through-browser.md)
