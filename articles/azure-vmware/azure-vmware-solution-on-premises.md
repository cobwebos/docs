---
title: 将 Azure VMware 解决方案连接到本地环境
description: 了解如何将 Azure VMware 解决方案连接到本地环境。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578649"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>将 Azure VMware 解决方案连接到本地环境

本文会继续使用[在规划期间收集的信息](production-ready-deployment-steps.md)将 Azure VMware 解决方案连接到本地环境。

在开始之前，请注意，将 Azure VMware 解决方案连接到本地环境有两个先决条件：

- 从本地环境到 Azure 的 ExpressRoute 线路。
- 用于 ExpressRoute Global Reach 对等互连的 /29 非重叠网络地址块（已在[规划阶段](production-ready-deployment-steps.md)定义）。

>[!NOTE]
> 你可以通过 VPN 进行连接，但这超出了本快速入门文档的介绍范围。

## <a name="establish-an-expressroute-global-reach-connection"></a>建立 ExpressRoute Global Reach 连接

若要使用 ExpressRoute Global Reach 与 Azure VMware 解决方案私有云建立本地连接，请按照[将本地环境与私有云对等互连](tutorial-expressroute-global-reach-private-cloud.md)教程进行操作。



## <a name="verify-on-premises-network-connectivity"></a>验证本地网络连接性

现在，应该会在本地边缘路由器中看到 ExpressRoute 连接 NSX-T 网段和 Azure VMware 解决方案管理段。

>[!NOTE]
>每个人都有不同的环境，有些人需要允许这些路由传播回本地网络。  

某些环境会使用防火墙来保护 ExpressRoute 线路。  如果没有防火墙，也没有进行路由修剪，则可以从本地环境 ping 通 Azure VMware Solution vCenter 服务器或 NSX-T 网段上的 [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment)。

此外，从 NSX-T 网段上的 VM 还可以访问本地环境中的资源。

## <a name="next-steps"></a>后续步骤

继续学习下一部分，以便部署和配置 VMware HCX

> [!div class="nextstepaction"]
> [部署和配置 VMware HCX](tutorial-deploy-vmware-hcx.md)