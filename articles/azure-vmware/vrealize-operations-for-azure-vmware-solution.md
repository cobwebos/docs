---
title: 为 Azure VMware 解决方案设置 vRealize 操作
description: 了解如何为 Azure VMware 解决方案私有云设置 vRealize 操作。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579738"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>为 Azure VMware 解决方案设置 vRealize 操作


vRealize Operations Manager 是一种操作管理平台，可让 VMware 基础结构管理员监视系统资源。 这些系统资源可能是应用程序级或基础结构级别 (物理和虚拟) 对象。 过去，大多数 VMware 管理员已使用 vRealize 操作来监视和管理 VMware 私有云组件– vCenter、ESXi、NSX-T、vSAN 和混合云扩展 (HCX) 。 每个 Azure VMware 解决方案私有云都预配了一个专用 vCenter、NSX-T、vSAN 和 HCX 部署。 

[在开始](#before-you-begin)和[先决条件](#prerequisites)之前进行全面检查。 接下来，我们将通过 Azure VMware 解决方案逐步介绍两个 vRealize Operations Manager 的典型部署拓扑：

> [!div class="checklist"]
> * [用于管理 Azure VMware 解决方案部署的本地 vRealize 操作](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware 解决方案部署上运行的 vRealize 操作](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>在开始之前
* 查看 [vRealize Operations Manager 产品文档](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) ，了解如何部署 vRealize 操作。 
* 查看基本的 Azure VMware 解决方案软件定义的 Datacenter (SDDC) [教程系列](tutorial-network-checklist.md)。
* （可选）查看用于管理 Azure VMware 解决方案部署选项的本地 vRealize 操作的 [VRealize 操作远程控制器](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 产品文档。 



## <a name="prerequisites"></a>必备条件
* 应在本地与 Azure VMware 解决方案 SDDC 之间配置 VPN 或 Azure ExpressRoute。
* Azure VMware 解决方案私有云已部署在 Azure 中。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>用于管理 Azure VMware 解决方案部署的本地 vRealize 操作
大多数客户都有 vRealize 操作的现有本地部署，用于管理一个或多个本地 Vcenter 域。 当客户在 Azure 中预配新的 Azure VMware 解决方案私有云时，他们通常使用 Azure ExpressRoute 或第3层 VPN 解决方案将其本地环境连接到 Azure VMware 解决方案，如下所示。   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="用于管理 Azure VMware 解决方案部署的本地 vRealize 操作"  border="false":::

若要将 vRealize 操作功能扩展到 Azure VMware 解决方案私有云，可 [为 Azure Vmware 解决方案私有云资源](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) 创建适配器实例-从 Azure vmware 解决方案私有云收集数据并将其引入本地 vRealize 操作。 本地 vRealize Operations Manager 实例可以直接连接到 Azure VMware 解决方案上的 vCenter 和 NSX-T 管理器，也可以选择在 Azure VMware 解决方案私有云上部署 vRealize 操作远程收集器。 VRealize 操作远程收集器会压缩并加密从 Azure VMware 解决方案私有云收集的数据，然后再通过 ExpressRoute 或 VPN 网络将其发送到本地运行的 vRealize Operations Manager。 

> [!TIP]
> 有关安装 vRealize Operations Manager 的循序渐进指南，请参阅 [VMware 文档](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware 解决方案部署上运行的 vRealize 操作

另一种部署选项是在 Azure VMware 解决方案私有云中的一个 vSphere 群集上部署 vRealize Operations Manager 的实例，如下所示。 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="用于管理 Azure VMware 解决方案部署的本地 vRealize 操作" border="false":::

部署 vRealize 操作的 Azure VMware 解决方案实例后，可将 vRealize 操作配置为从 vCenter、ESXi、NSX-T、vSAN 和 HCX 收集数据。 

> [!TIP]
> 有关安装 vRealize Operations Manager 的循序渐进指南，请参阅 [VMware 文档](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。


## <a name="known-limitations"></a>已知的限制

- **cloudadmin@vsphere.local**Azure VMware 解决方案中的用户拥有[有限的特权](concepts-role-based-access-control.md)。 Azure VMware 解决方案 (Vm) 的虚拟机不支持使用 VMware 工具进行来宾内内存收集。 在这种情况下，活动和消耗的内存使用率将继续工作。
- 基于主机的业务意向的工作负载优化不起作用，因为 Azure VMware 解决方案管理群集配置，包括 DRS 设置。
- 在 vRealize Operations Manager 8.0 和更高版本中，完全支持使用基于群集的业务意向在 SDDC 中放置跨群集的工作负荷。 但是，工作负荷优化并不知道资源池，而是将虚拟机放置在群集级别。 用户可以在 Azure VMware 解决方案 vCenter Server 界面中手动更正此项。
- 你无法使用 Azure VMware 解决方案 vCenter Server 凭据登录到 vRealize Operations Manager。 
- Azure VMware 解决方案不支持 vRealize Operations Manager 插件。

使用 vCenter Server 的云帐户将 Azure VMware 解决方案 vCenter 连接到 vRealize Operations Manager 时，你将遇到以下警告：

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="用于管理 Azure VMware 解决方案部署的本地 vRealize 操作":::

由于 **cloudadmin@vsphere.local** Azure VMware 解决方案中的用户没有足够的权限来执行注册所需的所有 vCenter Server 操作，因此会出现警告。 但是，这些权限足以使适配器实例进行数据收集，如下所示：

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="用于管理 Azure VMware 解决方案部署的本地 vRealize 操作":::

有关详细信息，请参阅 [配置 VCenter 适配器实例所需的特权](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)。

<!-- LINKS - external -->


<!-- LINKS - internal -->




