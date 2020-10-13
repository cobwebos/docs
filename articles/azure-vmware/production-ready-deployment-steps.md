---
title: 规划 Azure VMware 解决方案部署
description: 本文概述了 Azure VMware 解决方案部署工作流。  最终结果是一个就绪的可用于创建和迁移虚拟机 (VM) 的环境。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 1a9ff313243650cc3f9c44be2eb1c62da5557955
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578640"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>规划 Azure VMware 解决方案部署

在本文中，我们将为你提供规划过程，该过程用于标识并收集在部署过程中使用的数据。 [使用预部署清单](pre-deployment-checklist.md)来记录信息，方便在部署过程中进行参考。  

本快速入门中的过程会生成一个用于创建虚拟机 (VM) 并进行迁移的生产就绪环境。 

>[!IMPORTANT]
>在创建 Azure VMware 解决方案资源之前，你需要提交一个支持票证来分配你的节点。 支持团队收到你的请求后，最多需要五个工作日来确认你的请求并分配你的节点。 如果你有现有的 Azure VMware 解决方案私有云，但需要分配更多的节点，你会经历相同的过程。 有关详细信息，请参阅[如何启用 Azure VMware 解决方案资源](enable-azure-vmware-solution.md)。 


## <a name="ip-address-segment"></a>IP 地址段

规划部署的第一步是规划 IP 分段。  Azure VMware 解决方案引入你提供的一个 /22 网络。 然后将其划分为较小的段，并将这些 IP 段用于 vCenter、VMware HCX、NSX-T 和 vMotion。

Azure VMware 解决方案通过内部 ExpressRoute 线路连接到 Microsoft Azure 虚拟网络。 在大多数情况下，它通过 ExpressRoute Global Reach 连接到你的数据中心。 

通常情况下，Azure VMware 解决方案、你现有的 Azure 环境和本地环境都会交换路由。 这种情况下，在此步骤中定义的 /22 CIDR 网络地址块不应与本地或 Azure 中已有的任何地址块重叠。

**示例：** 10.0.0.0/22

有关详细信息，请参阅[网络规划清单](tutorial-network-checklist.md#routing-and-subnet-considerations)。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="标识 - IP 地址段" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>虚拟机工作负荷的 IP 地址段

标识一个 IP 段，以便在私有云中创建你的第一个网络（NSX 段）。  换句话说，你需要在 Azure VMware 解决方案上创建一个网段，以便可以将 VM 部署到 Azure VMware 解决方案。   

即使你只计划扩展 L2 网络，也可以创建一个可用来验证环境的网段。

请记住，创建的所有 IP 段在 Azure 和本地占用空间中必须是独一无二的。  

**示例：** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="标识 - IP 地址段" border="false":::     

## <a name="optional-extend-networks"></a>（可选）扩展网络

你可以将网段从本地扩展到 Azure VMware 解决方案。如果你这样做，请立即标识这些网络。  

请记住：

- 如果你计划从本地扩展网络，则这些网络必须连接到本地 VMware 环境中的 [vSphere 分布式交换机 (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html)。  
- 如果你要扩展的网络位于 [vSphere 标准交换机](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)上，则无法对其进行扩展。

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach 对等互连网络

标识 ExpressRoute Global Reach 对等互连所需的一个 `/29` CIDR 网络地址块。 请记住，创建的所有 IP 段在 Azure VMware 解决方案和本地占用空间中必须是独一无二的。 此段中的 IP 用于 ExpressRoute Global Reach 连接的每一端，可将 Azure VMware 解决方案 ExpressRoute 线路与本地 ExpressRoute 线路相连。 

**示例：** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="标识 - IP 地址段" border="false":::

## <a name="subscription"></a>订阅

标识你计划用来部署 Azure VMware 解决方案的订阅。  你可以创建新订阅，也可以重用现有的订阅。

>[!NOTE]
>订阅必须与一个 Microsoft 企业协议相关联。

## <a name="resource-group"></a>资源组

标识要用于 Azure VMware 解决方案的资源组。  通常会专门为 Azure VMware 解决方案创建一个资源组，但你可以使用现有的资源组。

## <a name="region"></a>区域

标识要部署 Azure VMware 解决方案的区域。  有关详细信息，请参阅[“可用 Azure 产品（按区域）”指南](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)。

## <a name="resource-name"></a>资源名称

定义要在部署过程中使用的资源名称。  资源名称是一个易记的描述性名称，用来为 Azure VMware 解决方案私有云命名。

## <a name="size-nodes"></a>节点大小

确定在部署 Azure VMware 解决方案时要使用的节点大小。  有关完整列表，请参阅 [Azure VMware 解决方案私有云和群集](concepts-private-clouds-clusters.md#hosts)文档。

## <a name="number-of-hosts"></a>主机数

定义要部署到 Azure VMware 解决方案私有云的主机数。  最小节点计数为 3 个，每个群集最多为 16 个。  有关详细信息，请参阅 [Azure VMware 解决方案私有云和群集](concepts-private-clouds-clusters.md#clusters)文档。

如果你的需要超出初始部署数目，以后可以随时扩展群集。

## <a name="vcenter-admin-password"></a>vCenter 管理员密码
定义 vCenter 管理员密码。  在部署过程中，你将创建一个 vCenter 管理员密码。 此密码在 vCenter 生成过程中用于 cloudadmin@vsphere.local 管理员帐户。 你将使用它来登录到 vCenter。

## <a name="nsx-t-admin-password"></a>NSX-T 管理员密码
定义 NSX-T 管理员密码。  在部署过程中，你将创建一个 NSX-T 管理员密码。 在 NSX 生成过程中，此密码将分配给 NSX 帐户中的管理员用户。 你将使用它登录 NSX-T Manager。

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>用于附加 Azure VMware 解决方案的 Azure 虚拟网络

若要访问 Azure VMware 解决方案私有云，Azure VMware 解决方案附带的 ExpressRoute 线路必须附加到 Azure 虚拟网络。  在部署过程中，你可以定义新的虚拟网络或选择现有的虚拟网络。

Azure VMware 解决方案中的 ExpressRoute 线路连接到在此步骤中定义的 Azure 虚拟网络中的 ExpressRoute 网关。  

>[!IMPORTANT]
>如果选择现有的虚拟网络，则必须选择一个没有预先存在的网关子网的虚拟网络。  

如果要将 ExpressRoute 线路从 Azure VMware 解决方案连接到现有 ExpressRoute 网关，可在部署后执行此操作。  

因此，综上所述，你是否要将 Azure VMware 解决方案连接到现有的 ExpressRoute 网关？  

* **是** = 标识在部署过程中不使用的虚拟网络。
* **否** = 标识一个现有虚拟网络，或者在部署过程中创建一个新的虚拟网络。

无论采用哪种方式，都可以记录要在此步骤中执行的操作。

>[!NOTE]
>你的本地环境和 Azure VMware 解决方案可以看到此虚拟网络，因此请确保在此虚拟网络和子网中使用的任何 IP 段都不重叠。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="标识 - IP 地址段" border="false":::



## <a name="vmware-hcx-network-segments"></a>VMware HCX 网段

VMware HCX 是与 Azure VMware 解决方案捆绑在一起的一项技术。 VMware HCX 的主要用例是工作负荷迁移和灾难恢复。 如果你计划执行上述任一操作，最好立即规划网络。   否则，可以跳到下一步。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>后续步骤
现在，你已收集并记录了所需的信息，请继续执行下一部分以创建 Azure VMware 解决方案私有云。

> [!div class="nextstepaction"]
> [部署 Azure VMware 解决方案](deploy-azure-vmware-solution.md)