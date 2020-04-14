---
title: 将 Azure 快速路由与 Oracle 云基础架构连接 |微软文档
description: 将 Azure 快速路由与 Oracle 云基础架构 （OCI） 快速连接连接，实现跨云 Oracle 应用程序解决方案
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263176"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>设置 Azure 和 Oracle 云基础结构之间的直接互连  

为了创造[集成的多云体验](oracle-oci-overview.md)，微软和甲骨文通过[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)在Azure和甲骨文云基础设施（OCI）之间提供直接互连。 通过 ExpressRoute 和 FastConnect 互连，客户可以体验低延迟、高吞吐量、两个云之间的私人直接连接。

> [!IMPORTANT]
> 在 2020 年 5 月之前，Oracle 将在 Azure/Oracle 云互连解决方案中验证这些应用程序在 Azure 中运行。
> * 电子商务套件
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * 甲骨文零售应用程序
> * 甲骨文海龙财务管理

下图显示了互连的高级概述：

![跨云网络连接](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>先决条件

* 要在 Azure 和 OCI 之间建立连接，必须具有活动 Azure 订阅和活动 OCI 租约。

* 仅当 Azure ExpressRoute 对等互连位置靠近或与 OCI FastConnect 位于同一对等位置时，才可能连接。 请参阅[区域可用性](oracle-oci-overview.md#region-availability)。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>配置快速路由和快速连接之间的直接连接

1. 在资源组下的 Azure 订阅上创建标准 ExpressRoute 电路。 
    * 创建 ExpressRoute 时，请选择**Oracle 云快速连接**作为服务提供商。 要创建 ExpressRoute 电路，请参阅分[步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 电路提供粒度带宽选项，而 FastConnect 支持 1、2、5 或 10 Gbps。 因此，建议在 ExpressRoute 下选择这些匹配的带宽选项之一。

    ![创建快速路由电路](media/configure-azure-oci-networking/exr-create-new.png)
1. 记下您的快速路由**服务密钥**。 在配置 FastConnect 电路时，您需要提供密钥。

    ![快速路由服务密钥](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦预配了 ExpressRoute 电路（即使未**预配****提供商状态**），您也会收取快递线路费用。

1. 雕刻出两个私有 IP 地址空间 /30，每个空间不与 Azure 虚拟网络或 OCI 虚拟云网络 IP 地址空间重叠。 我们将第一个 IP 地址空间称为主地址空间，第二个 IP 地址空间称为辅助地址空间。 记下配置 FastConnect 电路时所需的地址。
1. 创建动态路由网关 （DRG）。 创建 FastConnect 电路时，您需要这样做。 有关详细信息，请参阅[动态路由网关](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文档。
1. 在 Oracle 租户下创建 FastConnect 电路。 有关详细信息，请参阅 Oracle[文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * 在 FastConnect 配置下，选择**Microsoft Azure：快速路由**作为提供程序。
    * 选择上一步中预配的动态路由网关。
    * 选择要预配的带宽。 为了获得最佳性能，带宽必须与创建 ExpressRoute 电路时选择的带宽相匹配。
    * 在**提供程序服务密钥**中，粘贴 ExpressRoute 服务密钥。
    * 使用上一步中划分的第一个 /30 专用 IP 地址空间，用于**主 BGP IP 地址**，使用辅助**BGP IP**地址的第二 /30 专用 IP 地址空间。
        * 将 Oracle BGP IP 地址（主和辅助）的两个范围的第一个可使用地址和第二个地址分配给客户 BGP IP 地址（从快速连接的角度来看）。 第一个可使用的 IP 地址是 /30 地址空间中的第二个 IP 地址（第一个 IP 地址由 Microsoft 保留）。
    * 单击“创建”。 
1. 使用路由表，通过动态路由网关完成将 FastConnect 连接到 Oracle 租户下的虚拟云网络。
1. 导航到 Azure，并确保已预配 ExpressRoute 电路的**提供程序状态**更改为**预配**，并且已预配**Azure 私有**类型的对等互连。 这是以下步骤的先决条件。

    ![快速路由提供程序状态](media/configure-azure-oci-networking/exr-provider-status.png)
1. 单击**Azure 专用**对等互连。 您将看到已根据您在设置 FastConnect 电路时输入的信息自动配置对等互连详细信息。

    ![专用对等互连设置](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>将虚拟网络连接到快速路由

1. 创建虚拟网络和虚拟网络网关（如果尚未创建）。 有关详细信息，请参阅分[步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 通过执行[Terraform 脚本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或执行 PowerShell 命令来[配置 ExpressRoute 快速路径](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，设置虚拟网络网关和 ExpressRoute 电路之间的连接。

完成网络配置后，可以通过单击 Azure 门户中的 ExpressRoute 专用对等边栏选项卡下的 **"获取 ARP 记录**"和 **"获取路由"** 表来验证配置的有效性。

## <a name="automation"></a>自动化

Microsoft 已创建 Terraform 脚本，以实现网络互连的自动部署。 Terraform 脚本在执行之前需要使用 Azure 进行身份验证，因为它们需要对 Azure 订阅授予足够的权限。 可以使用[Azure 活动目录服务主体](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI 执行身份验证。 有关详细信息，请参阅[Terraform 文档](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

部署互连的 Terraform 脚本和相关文档可以在此[GitHub 存储库](https://aka.ms/azureociinterconnecttf)中找到。

## <a name="monitoring"></a>监视

在这两个云上安装代理，可以利用 Azure[网络性能监视器 （NPM）](../../../expressroute/how-to-npm.md)来监视端到端网络的性能。 NPM 可帮助您轻松识别网络问题，并帮助消除这些问题。

## <a name="delete-the-interconnect-link"></a>删除互连链接

要删除互连，必须按照给定的特定顺序执行以下步骤。 否则将导致"故障状态"ExpressRoute 电路。

1. 删除快速路由连接。 单击连接页面上的 **"删除**"图标，删除连接。 有关详细信息，请参阅[ExpressRoute 文档](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. 从 Oracle 云控制台中删除 Oracle 快速连接。
1. 删除 Oracle 快速连接电路后，可以删除 Azure ExpressRoute 电路。

此时，删除和取消预配过程已完成。

## <a name="next-steps"></a>后续步骤

* 有关 OCI 和 Azure 之间的跨云连接的详细信息，请参阅[Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用[Terraform 脚本](https://aka.ms/azureociinterconnecttf)通过 Azure 部署针对目标的 Oracle 应用程序的基础结构，并配置网络互连。 
