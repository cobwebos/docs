---
title: 为 VM 配置专用 IP 地址 - Azure 门户
description: 了解如何使用 Azure 门户为虚拟机配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707848"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>使用 Azure 门户为 VM 配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

完成以下示例步骤需要事先创建一个简单的环境。 若要运行本文档中所述的步骤，请先[创建一个虚拟网络](quick-create-portal.md#create-a-virtual-network)。 不过，在步骤 3 中，请改用以下值：

| 设置 | Value |
| ------- | ----- |
| 名称 | *TestVNet* |
| 地址空间 | *192.168.0.0/16* |
| 资源组 | **TestRG**（如果需要，请选择“新建”以创建资源组）  |
| 子网 - 名称 | *FrontEnd* |
| 子网 - 地址范围 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>创建用于测试静态专用 IP 地址的 VM
在资源管理器部署模式下创建 VM 时，不能使用 Azure 门户设置静态专用 IP 地址。 应该先创建 VM。 然后，可将其专用 IP 设置为静态。

若要在名为 TestVNet 的虚拟网络的前端子网中创建名为 DNS01 的 VM，请按以下步骤操作    ：

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“创建资源”  。

    ![创建资源，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. 选择 "**计算**" "  >  **虚拟机**"。

    ![创建 VM，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. 在“基本信息”中，按下表中所述指定项的值。  然后依次选择“下一步: 磁盘”、“下一步: 网络”。 **&nbsp;&nbsp;** **&nbsp;&nbsp;**

    | 项目 | Value |
    | --- | --- |
    | **订阅** | 你的当前订阅 |
    | **资源组** | **TestRG**（从下拉列表中选择） |
    | **虚拟机名称** | *DNS01* |
    | **区域** | **（美国）美国东部** |
    | **映像** | **Windows Server 2019 Datacenter** |
    | **大小** | “VM 大小”为“B1ls”，“产品/服务”为“标准”     |
    | **用户名** | 你的管理员帐户的用户名 |
    | **密码** | 你的管理员帐户用户名的密码 |
    | **确认密码** | 再次输入密码 |

    ![“基本信息”选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. 在“网络”中，按下表中所述指定项的值，然后选择“下一步”。  

    | 项目 | Value |
    | --- | --- |
    | **虚拟网络** | **TestVNet** |
    | **子网** | **FrontEnd** |

    ![“网络”选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. 在“管理”中的“诊断存储帐户”下，选择“vnetstorage”。    如果该存储帐户未显示在列表中，请选择“新建”，指定“vnetstorage”作为名称，然后选择“确定”。     最后，选择“查看 + 创建” **&nbsp;&nbsp;** 。

    ![“管理”选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. 在“查看 + 创建”中检查概述信息，然后选择“创建”。  

    ![“查看 + 创建”选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

创建 VM 后，会显示以下消息。

![“部署完成”消息，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>检索 VM 的专用 IP 地址信息
若要查看新 VM 的专用 IP 地址信息，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com)以找到你的 VM。 搜索并选择“虚拟机”  。

    ![虚拟机，搜索框，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 选择新 VM 的名称 (DNS01)。 

    ![虚拟机列表，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. 选择“网络”，然后选择列出的唯一网络接口。 

    ![网络接口，网络，虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. 选择“IP 配置”，然后选择表中列出的 IP 配置。 

    ![IP 配置，网络接口，网络，虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. 在“专用 IP 地址设置”中的“TestVNet/FrontEnd”虚拟网络/子网下，记下“分配”值（“动态”或“静态”）和“IP 地址”。      

    ![动态或静态分配，旧的专用 IP 地址设置，IP 配置，网络接口，网络，虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>将静态专用 IP 地址添加到现有 VM
若要将静态专用 IP 地址添加到新 VM，请执行以下操作：

1. 在“IP 配置”页中，将专用 IP 地址的分配设置为“静态”。 
2. 将专用 IP 地址更改为 192.168.1.101，然后选择“保存”。   
   
    ![动态或静态分配，新的专用 IP 地址设置，IP 配置，网络接口，网络，虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> 选择“保存”后，如果注意到分配仍设置为“动态”，则表示键入的 IP 地址已被使用   。 请尝试输入其他 IP 地址。

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>从 VM 中删除静态专用 IP 地址
若要从 VM 中删除静态专用 IP 地址，请执行以下操作：

在“IP 配置”页中，将专用 IP 地址的分配设置为“动态”，然后选择“保存”。  

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

在 VM 的操作系统中，不应以静态方式分配已分配给 Azure VM 的专用 IP。  仅在必要的情况下（例如，要[向 VM 分配许多 IP 地址](virtual-network-multiple-ip-addresses-portal.md)时），才执行专用 IP 的静态分配。 如果在操作系统中手动设置专用 IP 地址，请确保该 IP 地址与分配给 Azure [网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址相匹配。 否则，与 VM 的连接可能会断开。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

另外，切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。 

## <a name="next-steps"></a>后续步骤

了解如何管理 [IP 地址设置](virtual-network-network-interface-addresses.md)。
