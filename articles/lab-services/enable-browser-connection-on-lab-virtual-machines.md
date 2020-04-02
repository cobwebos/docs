---
title: 在 Azure 开发人员测试实验室虚拟机上启用浏览器连接 |微软文档
description: DevTest Labs 现在与 Azure Bastion 集成，作为实验室的所有者，您可以通过浏览器启用访问所有实验室虚拟机。
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
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549084"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>在实验室虚拟机上启用浏览器连接 

DevTest 实验室与[Azure 堡垒](https://docs.microsoft.com/azure/bastion/)集成 ，使您能够通过浏览器连接到虚拟机。 您首先需要在实验室虚拟机上启用浏览器连接。

作为实验室的所有者，您可以通过浏览器启用访问所有实验室虚拟机。 无需额外的客户端、代理或软件。 Azure 堡垒通过 TLS 直接在 Azure 门户中为虚拟机提供安全、无缝的 RDP/SSH 连接。 通过 Azure 堡垒进行连接时，虚拟机不需要公共 IP 地址。 有关详细信息，请参阅什么是[Azure 堡垒？](../bastion/bastion-overview.md)

> [!NOTE]
> 在实验室虚拟机上启用浏览器连接处于预览状态。

本文演示如何在实验室虚拟机上启用浏览器连接。

## <a name="prerequisites"></a>先决条件 
在现有实验室的虚拟网络 **（OR）** 中部署堡垒主机，将实验室与已配置的 VNet 进行连接。 

要了解如何在 VNet 中部署堡垒主机，请参阅[创建 Azure 堡垒主机（预览）。](../bastion/bastion-create-host-portal.md) 创建堡垒主机时，选择实验室的虚拟网络。 

要了解如何将实验室与已配置的 VNet 的堡垒连接，请参阅[在 Azure 开发人员测试实验室中配置虚拟网络](devtest-lab-configure-vnet.md)。 选择部署了堡垒主机的 VNet 和其中的**Azure Bastion Subnet。** 以下为详细步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择左侧导航菜单上**的所有服务**。 
1. 从列表中选择**开发人员测试实验室**。 
1. 从实验室列表中，选择*您的实验室*。 

    > [!NOTE]
    > Azure 堡垒当前处于预览状态。 它仅限于以下区域：美国西部、美国东部、西欧、美国中南部、澳大利亚东部和日本东部。 因此，如果您的实验室不在其中一个区域中，请在这些区域之一创建一个实验室。 
1. 在左侧菜单的 **"设置"** 部分中选择 **"配置"和"策略**"。 
1. 选择**虚拟网络**。
1. 从工具栏中选择 **"添加**"。 
1. 选择已部署堡垒主机的**VNet。** 
1. 选择子网 **：AzureBastion 子网**。 

    ![子网](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. 选择 **"虚拟机创建中的使用**"选项。 
1. 在工具栏上选择“保存”。**** 
1. 如果您有实验室的旧 VNet，请通过选择 "*...* 并**删除**。 

## <a name="enable-browser-connection"></a>启用浏览器连接 

在实验室内配置了堡垒 VNet 后，作为实验室所有者，您可以在实验室虚拟机上启用浏览器连接。

要在实验室虚拟机上启用浏览器连接，请按照以下步骤操作：

1. 在 Azure 门户中，导航到*实验室*。
1. 选择“配置和策略”。****
1. 在 **"设置"** 中，选择**浏览器连接（预览）。**

![启用浏览器连接](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何使用浏览器连接到 VM：[通过浏览器连接到虚拟机](connect-virtual-machine-through-browser.md)