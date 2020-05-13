---
title: Azure 防火墙强制隧道
description: 你可以配置强制隧道来将 Internet 绑定的流量路由到其他防火墙或网络虚拟设备，以便进一步处理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 463bccb29d59f06e7381d7d7123946029223a93a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199721"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure 防火墙强制隧道

你可以对 Azure 防火墙进行配置，使其将所有 Internet 绑定的流量路由到指定的下一跃点，而不是直接前往 Internet。 例如，你可能有本地边缘防火墙或其他网络虚拟设备 (NVA) 用来在将网络流量传递到 Internet 之前对其进行处理。

默认情况下，Azure 防火墙不允许强制隧道，以确保满足所有的出站 Azure 依赖关系。 禁用了不直接连接到 Internet 的默认路由的*AzureFirewallSubnet*上的用户定义路由（UDR）配置。

## <a name="forced-tunneling-configuration"></a>强制隧道配置

为了支持强制隧道，服务管理流量与客户流量分开。 还需要一个名为*AzureFirewallManagementSubnet* （最小子网大小/26）的专用子网，其自身关联的公共 IP 地址。 此子网上允许的唯一路由是到 Internet 的默认路由，并且必须禁用 BGP 路由传播。

如果有通过 BGP 播发的默认路由来强制向本地通信，则必须先创建*AzureFirewallSubnet*和*AzureFirewallManagementSubnet* ，然后再部署防火墙，并将 UDR 与 Internet 建立默认路由，并禁用**虚拟网络网关路由传播**。

在此配置中， *AzureFirewallSubnet*现在可以包含到任何本地防火墙或 NVA 的路由，以便在将流量传递到 Internet 之前处理流量。 如果在此子网上启用了**虚拟网络网关路由传播**，还可以通过 BGP 将这些路由发布到*AzureFirewallSubnet* 。

例如，可以在*AzureFirewallSubnet*上创建一个默认路由，将 VPN 网关作为下一跃点来访问本地设备。 或者，你可以启用**虚拟网络网关路由传播**，以获取到本地网络的相应路由。

![虚拟网络网关路由传播](media/forced-tunneling/route-propagation.png)

一旦将 Azure 防火墙配置为支持强制隧道，就不能撤消配置了。 如果删除防火墙上的所有其他 IP 配置，也会删除管理 IP 配置，并解除防火墙的分配。 无法删除分配给管理 IP 配置的公共 IP 地址，但可以分配不同的公共 IP 地址。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)
