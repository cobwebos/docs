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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461542"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>使用 Azure 门户为 VM 配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下示例步骤期望已创建一个简单的环境。 如果要在本文档中显示的步骤中运行这些步骤，请先[创建虚拟网络](quick-create-portal.md#create-a-virtual-network)。 但是，在步骤 3 中，改用这些值：

| 设置 | 值 |
| ------- | ----- |
| 名称 | *TestVNet* |
| 地址空间 | *192.168.0.0/16* |
| 资源组 | **TestRG（** 如有必要，选择 **"创建新**"以创建它） |
| 子网 - 名称 | *前端* |
| 子网 - 地址范围 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>创建用于测试静态专用 IP 地址的 VM
在资源管理器部署模式下创建 VM 时，无法使用 Azure 门户设置静态专用 IP 地址。 相反，您首先创建 VM。 然后，您可以将其专用 IP 设置为静态。

要在名为*TestVNet*的虚拟网络*的 FrontEnd*子网中创建名为*DNS01*的 VM，请按照以下步骤操作：

1. 在[Azure 门户](https://portal.azure.com)菜单中，选择 **"创建资源**"。

    ![创建资源 Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. 选择**计算** > **虚拟机**。

    ![创建 VM、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. 在 **"基础知识"** 中，指定下表中所述的项的值。 然后选择 **"&nbsp;下&nbsp;一步：磁盘"，** 然后**选择"下一个&nbsp;：&nbsp;网络**"。

    | Item | 值 |
    | --- | --- |
    | **订阅** | 您当前的订阅 |
    | **资源组** | **TestRG（** 从下拉列表中选择） |
    | **虚拟机名称** | *DNS01* |
    | **区域** | **（美国）美国东部** |
    | **映像** | **视窗服务器 2019 数据中心** |
    | **大小** | **B1ls**的**VM 大小**，**Offering****提供标准** |
    | **用户名** | 管理员帐户的用户名 |
    | **密码** | 管理员帐户用户名的密码 |
    | **确认密码** | 再次使用密码 |

    ![基础知识选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. 在 **"网络"** 中，指定下表中所述项的值，然后选择 **"下一步**"。

    | Item | 值 |
    | --- | --- |
    | **虚拟网络** | **TestVNet** |
    | **子网** | **前端** |

    ![网络选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. 在 **"管理**"下，**在诊断存储帐户**下，选择**vnetstorage**。 如果该存储帐户未显示在列表中，请选择"**创建新**"，指定*vnetstorage***的名称**，然后选择 **"确定**"。 最后，选择 **"&nbsp;+&nbsp;审阅创建**"。

    ![管理选项卡，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. 在 **"审阅 " 创建**中，查看概览信息，然后选择 **"创建**"。

    ![查看 + 创建选项卡、创建虚拟机、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

创建 VM 后将显示以下消息。

![部署完成消息，创建虚拟机，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>检索 VM 的专用 IP 地址信息
要查看新 VM 的专用 IP 地址信息，请处理：

1. 转到[Azure 门户](https://portal.azure.com)以查找 VM。 搜索并选择**虚拟机**。

    ![虚拟机、搜索框、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 选择新 VM 的名称 （**DNS01**）。

    ![虚拟机列表，Azure 门户](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. 选择 **"网络**"，然后选择列出的唯一网络接口。

    ![网络接口、网络、虚拟机、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. 选择**IP 配置**，然后选择表中列出的 IP 配置。

    ![IP 配置、网络接口、网络、虚拟机、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. 在**专用 IP 地址设置**中，在**TestVNet/FrontEnd**虚拟网络/子网下，请注意**分配**值（**动态**或**静态**）和**IP 地址**。

    ![动态或静态分配、旧的专用 IP 地址设置、IP 配置、网络接口、网络、虚拟机、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>将静态专用 IP 地址添加到现有 VM
要向新 VM 添加静态专用 IP 地址，请处理：

1. 在 IP 配置页中，将专用 IP 地址的分配设置为**静态**。
2. 将专用**IP 地址**更改为*192.168.1.101*，然后选择 **"保存**"。
   
    ![动态或静态分配、新的专用 IP 地址设置、IP 配置、网络接口、网络、虚拟机、Azure 门户](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> 如果在选择 **"保存"** 后注意到分配仍设置为 **"动态**"，则键入的 IP 地址已在使用中。 请尝试另一个 IP 地址。

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>从 VM 中删除静态专用 IP 地址
要从 VM 中删除静态专用 IP 地址，请：

在 IP 配置页中，将专用 IP 地址的分配设置为**动态**，然后选择 **"保存**"。

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

在 VM 的操作系统中，不应静态地分配分配给 Azure VM 的*专用*IP。 仅在必要时（如[将许多 IP 地址分配给 VM](virtual-network-multiple-ip-addresses-portal.md)时）进行私有 IP 的静态分配。 如果在操作系统中手动设置专用 IP 地址，请确保它与分配给 Azure[网络接口](virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址匹配。 否则，您可能会失去与 VM 的连接。 详细了解[专用 IP 地址](virtual-network-network-interface-addresses.md#private)设置。

此外，绝不应手动分配分配给虚拟机内 Azure 虚拟机*的公共*IP 地址。

## <a name="next-steps"></a>后续步骤

了解如何管理 [IP 地址设置](virtual-network-network-interface-addresses.md)。
