---
title: VMware 解决方案（按云简单 - Azure 网络连接）
description: 了解如何将 Azure 虚拟网络连接到云简单区域网络
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025072"
---
# <a name="azure-network-connections-overview"></a>Azure 网络连接概述

在区域中创建 CloudSimple 服务并创建节点时，可以：

* 请求 Azure 快速路由电路并将其附加到该区域的 CloudSimple 网络。
* 使用 Azure ExpressRoute 将云简单区域网络连接到 Azure 虚拟网络或本地网络。
* 从私有云环境提供对 Azure 订阅中运行的服务或本地网络的访问。

ExpressRoute 连接是高带宽和低延迟。

## <a name="benefits"></a>优点

Azure 网络连接允许您：

* 使用 Azure 作为私有云上虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器以加密私有云 vSAN 数据存储。
* 使用混合应用程序，其中应用程序的 Web 层在公共云中运行，而应用程序和数据库层在私有云中运行。

## <a name="azure-virtual-network-connection"></a>Azure 虚拟网络连接

私有云可以使用 ExpressRoute 连接到 Azure 资源。  通过 ExpressRoute 连接，您可以从私有云访问 Azure 订阅中运行的资源。  此连接允许您将私有云网络扩展到 Azure 虚拟网络。  云简单网络的路由将通过 BGP 与 Azure 虚拟网络交换。  如果配置了虚拟网络对等互连，则所有对等虚拟网络都将从 CloudSimple 网络访问。

![Azure 快速路由连接到虚拟网络](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>与本地网络的快速路由连接

您可以将现有的 Azure 快速路由电路连接到云简单区域。 ExpressRoute 全局覆盖功能用于将两个电路相互连接。  在本地和云简单快速路由电路之间建立连接。  此连接允许您将本地网络扩展到私有云网络。 来自云简单网络的路由将通过 BGP 与本地网络交换。

![本地快速路由连接 - 全球覆盖](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>连接到本地网络和 Azure 虚拟网络

与本地网络和 Azure 虚拟网络的连接可以从 CloudSimple 网络共存。  该连接使用 BGP 交换本地网络、Azure 虚拟网络和 CloudSimple 网络之间的路由。  当您在存在全局覆盖连接的情况下将 CloudSimple 网络连接到 Azure 虚拟网络时，Azure 虚拟网络路由将在本地网络上可见。  路由交换在 Azure 中发生在边缘路由器之间。

![与 Azure 虚拟网络连接的本地快速路由连接](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要注意事项

从本地网络和 Azure 虚拟网络连接到 CloudSimple 网络允许在所有网络之间进行路由交换。

* Azure 虚拟网络将从本地网络和云简单网络可见。
* 如果从本地网络连接到 Azure 虚拟网络，则使用全局覆盖连接到 CloudSimple 网络将允许从 CloudSimple 网络访问虚拟网络。
* 子网地址**不得**在连接的任何网络之间重叠。
* CloudSimple**不会**通告到 ExpressRoute 连接的默认路由
* 如果本地路由器通告默认路由，则来自 CloudSimple 网络和 Azure 虚拟网络的流量将使用通告的默认路由。  因此，无法使用公共 IP 地址访问 Azure 上的虚拟机。

## <a name="next-steps"></a>后续步骤

* [使用快速路由将 Azure 虚拟网络连接到云简单](virtual-network-connection.md)
* [使用 ExpressRoute 从本地连接到云简单](on-premises-connection.md)
