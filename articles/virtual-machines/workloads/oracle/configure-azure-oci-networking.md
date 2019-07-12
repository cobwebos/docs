---
title: Oracle 云基础结构与连接的 Azure ExpressRoute |Microsoft Docs
description: 将 Azure ExpressRoute 连接与 Oracle 云基础结构 (OCI) FastConnect，若要启用跨云 Oracle 应用程序解决方案
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707565"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>设置 Azure 与 Oracle 云基础结构之间直接相互连接  

若要创建[集成的多云体验](oracle-oci-overview.md)（预览版）、 Microsoft 和 Oracle 提供直接相互连接 Azure 和 Oracle 云基础结构 (OCI) 通过之间[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)。 通过 ExpressRoute 和 FastConnect 互相连接的桥梁，客户可能会遇到低延迟、 高吞吐量、 两个云之间进行专用直接连接。

> [!IMPORTANT]
> Microsoft Azure 和 OCI 之间的连接处于预览阶段。 若要启用 Azure 与 OCI 之间的低延迟连接，Azure 订阅和 OCI 租户必须先为此功能已列入允许列表。

下图显示了互相连接的桥梁的高级概述：

![跨云网络连接](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>先决条件

* 若要建立 Azure 和 OCI 之间的连接，必须具有有效的 Azure 订阅和 active OCI 租户。

* 连接才可能其中的 Azure ExpressRoute 对等互连位置为接近或 OCI FastConnect 与相同的对等互连位置中。 请参阅[预览版限制](oracle-oci-overview.md#preview-limitations)。

* 你的 Azure 订阅必须是已列入允许列表的此预览功能。 请联系 Microsoft 代表联系以启用此功能在你的订阅。

* OCI 租户必须是已列入允许列表的此预览功能。 有关详细信息，请与你的 Oracle 代表联系。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>配置 ExpressRoute 和 FastConnect 之间进行直接连接

1. Azure 订阅资源组下创建标准 ExpressRoute 线路。 
    * 在创建 ExpressRoute 时，选择**Oracle 云 FastConnect**作为服务提供商。 若要创建 ExpressRoute 线路，请参阅[分步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 线路提供了精细的带宽选项，而 FastConnect 支持 1、 2、 5 或 10 Gbps。 因此，建议选择 ExpressRoute 下的这些匹配的带宽选项之一。

    ![创建 ExpressRoute 线路](media/configure-azure-oci-networking/exr-create-new.png)
1. 记下 ExpressRoute**服务密钥**。 您需要配置 FastConnect 线路时提供的密钥。

    ![ExpressRoute 服务密钥](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 只要预配 ExpressRoute 线路将计费的 ExpressRoute 费用 (即使**提供程序状态**是**未预配**)。

1. 指定的每个均与你的 Azure 虚拟网络或 OCI 虚拟云网络 IP 地址空间不重叠的/30 两个专用 IP 地址空间。 我们将引用作为主地址空间的第一个 IP 地址空间和作为辅助地址空间的第二个 IP 地址空间。 请记下的地址，配置 FastConnect 线路时需要。
1. 创建动态路由网关 (DRG)。 创建 FastConnect 线路时，将需要此。 有关详细信息，请参阅[动态路由网关](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文档。
1. 创建 Oracle 租户下的 FastConnect 线路。 有关详细信息，请参阅[Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * FastConnect 配置下，选择**Microsoft Azure:ExpressRoute**作为提供程序。
    * 在上一步中选择动态路由网关预配。
    * 选择要将用户预配的带宽。 为了获得最佳性能，带宽必须匹配创建 ExpressRoute 线路时选择的带宽。
    * 在中**提供程序服务密钥**，粘贴 ExpressRoute 服务密钥。
    * 专用 IP 地址空间划分为上一步中使用第一个/30**主要 BGP IP 地址**和第二个/30 专用 IP 地址空间**辅助 BGP IP**地址。
        * 将分配两个范围的第一个可用地址为 Oracle BGP IP 地址 （主要或次要） 和第二个地址给客户 BGP IP 地址 （从 FastConnect 角度看）。 第一个可用 IP 地址是/30 中的第二个 IP 地址的地址的空间 （由 Microsoft 保留的第一个 IP 地址）。
    * 单击“创建”。 
1. 完成将 FastConnect 链接到你的 Oracle 租户通过动态路由网关，使用路由表下的虚拟云网络。
1. 导航到 Azure，并确保**提供程序状态**为 ExpressRoute 线路已更改为**已预配**和该类型的对等互连**Azure 专用**已预配。 这是用于执行以下步骤的先决条件。

    ![ExpressRoute 提供程序状态](media/configure-azure-oci-networking/exr-provider-status.png)
1. 单击**Azure 专用**对等互连。 你将看到的对等互连的详细信息自动配置基于向上 FastConnect 线路输入时设置的信息。

    ![专用对等互连设置](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>连接到 ExpressRoute 的虚拟网络

1. 如果你尚未，创建虚拟网络和虚拟网络网关。 有关详细信息，请参阅[分步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 设置虚拟网络网关和 ExpressRoute 线路之间的连接通过执行[Terraform 脚本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或通过执行 PowerShell 命令，为[配置 ExpressRoute 快速](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。

完成网络配置后，您可以通过单击验证你的配置的有效性**获取 ARP 记录**并**获取路由表**下的 ExpressRoute 专用对等互连边栏选项卡在 Azure 门户中。

## <a name="automation"></a>自动化

Microsoft 已创建 Terraform 脚本，以实现网络互连的自动化的部署。 Terraform 脚本需要在执行之前，Azure 身份验证因为它们需要 Azure 订阅的足够权限。 可以使用执行身份验证[Azure Active Directory 服务主体](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI。 有关详细信息，请参阅[Terraform 文档](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

在此可以找到的 Terraform 脚本和相关的文档来部署 inter-connect [GitHub 存储库](https://aka.ms/azureociinterconnecttf)。

## <a name="monitoring"></a>监视

在这两个云上安装代理，你可以利用 Azure[网络性能监视器 (NPM)](../../../expressroute/how-to-npm.md)监视端到端网络的性能。 NPM 可帮助你轻松地识别出网络问题，并帮助消除这些问题。

## <a name="delete-the-interconnect-link"></a>删除互连链接

若要删除的互连，则执行以下步骤必须是，按给定的特定顺序。 如果不这样做将导致"故障的状态"ExpressRoute 线路。

1. 删除 ExpressRoute 连接。 通过单击删除连接**删除**连接页上的图标。 有关详细信息，请参阅[ExpressRoute 文档](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. 从 Oracle 云控制台中删除 Oracle FastConnect。
1. 删除 Oracle FastConnect 线路后，可以删除 Azure ExpressRoute 线路。

在此情况下，删除和取消预配过程已完成。

## <a name="next-steps"></a>后续步骤

* 有关 OCI 和 Azure 之间的跨云连接的详细信息，请参阅[Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用[Terraform 脚本](https://aka.ms/azureociinterconnecttf)通过 Azure，部署目标 Oracle 应用程序的基础结构和配置网络互连。 