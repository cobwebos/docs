---
title: 创建具有静态公共 IP 地址的 VM - Azure 门户 | Microsoft 文档
description: 了解如何使用 Azure 门户创建具有静态公共 IP 地址的 VM。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 3ef96c83b460346ba49051f6f5c4fd0dd5df34ab
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790161"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 门户创建具有静态公共 IP 地址的虚拟机

可以创建具有静态公共 IP 地址的虚拟机。 使用公共 IP 地址可以通过 Internet 来与虚拟机通信。 分配静态公共 IP 地址而非动态地址可以确保地址永远不会改变。 详细了解[静态公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要将分配给现有虚拟机的公共 IP 地址从动态更改为静态，或者要使用专用 IP 地址，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。 公共 IP 地址会产生[少许费用](https://azure.microsoft.com/pricing/details/ip-addresses)，可为每个订阅使用的公共 IP 地址数有[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 选择 Azure 门户左上角的“+ 创建资源”  。
2. 选择“计算”，然后选择“Windows Server 2016 VM”或另一操作系统。********
3. 输入或选择以下信息，保留剩下的默认设置，然后选择“确定”  ：

    |设置|值|
    |---|---|
    |名称|myVM|
    |用户名| 输入所选用户名。|
    |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    |订阅| 选择订阅。|
    |资源组| 选择“使用现有资源组”，再选择“myResourceGroup”   。|
    |位置| 选择**美国东部**|

4. 选择 VM 的大小，然后选择“选择”  。
5. 在“设置”下选择“公共 IP 地址”。  
6. 输入“myPublicIpAddress”，选择“静态”，然后选择“确定”，如下图所示：   

   ![选择“静态”](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   如果公共 IP 地址必须为标准 SKU，请在“SKU”下选择“标准”。   详细了解[公共 IP 地址 SKU](virtual-network-ip-addresses-overview-arm.md#sku)。 如果虚拟机将添加到公共 Azure 负载均衡器的后端池，则虚拟机公共 IP 地址的 SKU 必须与负载均衡器的公共 IP 地址的 SKU 相匹配。 有关详细信息，请参阅 [Azure 负载均衡器](../load-balancer/skus.md)。

6. 在“选择公共入站端口”下选择一个端口，或者不选择任何端口。  选择了端口 3389，目的是能够通过 Internet 对 Windows Server 虚拟机进行远程访问。 对于生产型工作负荷，建议不要打开从 Internet 进行访问的端口 3389。

   ![选择端口](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. 接受其余默认设置，然后选择“确定”  。
8. 在“摘要”  页中，选择“创建”  。 部署虚拟机需要数分钟。
9. 部署虚拟机以后，请在门户顶部的搜索框中输入 *myPublicIpAddress*。 当“myPublicIpAddress”出现在搜索结果中时，将其选中。 
10. 可以查看分配的公共 IP 地址，以及分配给 **myVM** 虚拟机的地址，如下图所示：

    ![查看公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure 从你在其中创建虚拟机的区域使用的地址中分配了一个公共 IP 地址。 对于 Azure [公有](https://www.microsoft.com/download/details.aspx?id=56519)云、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)云、[中国](https://www.microsoft.com/download/details.aspx?id=57062)云和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云，可以下载范围（前缀）的列表。

11. 选择“配置”，确认分配为“静态”。  

    ![查看公共 IP 地址](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> 不要修改虚拟机操作系统中的 IP 地址设置。 操作系统不知道 Azure 公共 IP 地址。 虽然可以向操作系统添加专用 IP 地址设置，但除非必要，否则我们建议不要这样做，而只能阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之后才执行此操作。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”   。 当在搜索结果中看到“myResourceGroup”时，将其选中。 
2. 选择“删除资源组”  。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。   

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)
- 详细了解[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)
- 详细了解如何创建 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机