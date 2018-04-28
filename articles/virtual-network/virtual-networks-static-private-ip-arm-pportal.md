---
title: 为 VM 配置专用 IP 地址 - Azure 门户 | Microsoft 文档
description: 了解如何使用 Azure 门户为虚拟机配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff9d025980f80cb77246ea12dbf2e9bcedd73f86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>使用 Azure 门户为虚拟机配置专用 IP 地址

> [!div class="op_single_selector"]
> * [Azure 门户](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure 门户（经典）](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell（经典）](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI（经典）](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍 Resource Manager 部署模型。 还可以[管理经典部署模型中的静态专用 IP 地址](virtual-networks-static-private-ip-classic-pportal.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

需要已创建一个简单的环境才能执行以下示例步骤。 若要运行本文档中所显示的步骤，请先生成[创建虚拟网络](quick-create-portal.md) 中所述的测试环境。

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>如何创建用于测试静态专用 IP 地址的 VM
在 Resource Manager 部署模型中创建 VM 时，不能使用 Azure 门户设置静态专用 IP 地址。 必须先创建 VM，然后再将其专用 IP 设置为静态。

若要在名为 TestVNet 的 VNet 的前端子网中创建名为 DNS01 的 VM，请按以下步骤操作：

1. 在浏览器中导航到 http://portal.azure.com，并根据需要使用 Azure 帐户登录。
2. 单击“创建资源” > “计算” > “Windows Server 2012 R2 数据中心”，确认“选择部署模型”列表已显示“资源管理器”，然后单击“创建”，如下图所示。
   
    ![在 Azure 门户中创建 VM](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. 在“基本信息”窗格中，输入要创建的 VM 的名称（在此方案中为DNS01）、本地管理员帐户和密码，如下图所示。
   
    ![“基本信息”窗格](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. 请确保所选“位置”为“美国中部”，然后在“资源组”下单击“选择现有项”，接着依次单击“资源组”、“TestRG””和“确定”。
   
    ![“基本信息”窗格](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. 在“选择大小”窗格中，选择“A1 标准”，然后单击“选择”。
   
    ![选择“大小”窗格](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. 在“设置”窗格中，确保属性设置为以下值，然后单击“确定”。
   
    -“存储帐户”：*vnetstorage*
   
   * **网络**：*TestVNet*
   * **子网**：*FrontEnd*
     
     ![选择“大小”窗格](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. 在“摘要”窗格中，单击“确定”。 请注意，以下磁贴会显示在仪表板中。
   
    ![在 Azure 门户中创建 VM](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-portal.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。 切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何检索 VM 的静态专用 IP 地址信息
若要查看使用以上步骤创建的 VM 的静态专用 IP 地址信息，请执行以下步骤。

1. 在 Azure 门户中，单击“全部浏览” > “虚拟机” > “DNS01” > “所有设置” > “网络接口”，然后单击所列出的唯一网络接口。
   
    ![部署 VM 磁贴](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. 在“网络接口”窗格中，单击“所有设置” > “IP 地址”并几下“分配”和“IP 地址”值。
   
    ![部署 VM 磁贴](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何将静态专用 IP 地址添加到现有 VM
要将静态专用 IP 地址添加到使用上面步骤创建的 VM 中，请按照以下步骤操作：

1. 从上面所示的“IP 地址”窗格中，单击“分配”下的“静态”。
2. 键入 *192.168.1.101* 作为“**IP 地址**”，并单击“**保存**”。
   
    ![在 Azure 门户中创建 VM](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> 单击“保存”后，如果注意到分配仍设置为“动态”，则表示你键入的 IP 地址已被使用。 请尝试其他 IP 地址。
> 
> 

我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-portal.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。 切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何从 VM 中删除静态专用 IP 地址
若要从上面创建的 VM 中删除静态专用 IP 地址，请完成以下步骤：

从上面所示的“IP 地址”窗格中，单击“分配”下的“动态”，然后单击“保存”。

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

我们建议，除非有必要（例如，[为 Windows VM 分配多个 IP 地址](virtual-network-multiple-ip-addresses-portal.md)时），否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。 切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。

## <a name="next-steps"></a>后续步骤

了解如何管理 [IP 地址设置](virtual-network-network-interface-addresses.md)。

