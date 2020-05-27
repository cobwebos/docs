---
title: Azure 防火墙强制隧道
description: 可以对强制隧道进行配置，将发往 Internet 的流量路由到其他防火墙或网络虚拟设备进行进一步处理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/11/2020
ms.author: victorh
ms.openlocfilehash: 463bccb29d59f06e7381d7d7123946029223a93a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199721"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure 防火墙强制隧道

可以对 Azure 防火墙进行配置，将所有发往 Internet 的流量路由到指定的下一个跃点，而不是直接转到 Internet。 例如，你可能有一个本地边缘防火墙或其他网络虚拟设备 (NVA)，用于对网络流量进行处理，然后再将其传递到 Internet。

默认情况下，Azure 防火墙不允许强制隧道，以确保满足所有的出站 Azure 依赖关系。 AzureFirewallSubnet 上的其默认路由不直接指向 Internet 的用户定义路由 (UDR) 配置处于禁用状态。

## <a name="forced-tunneling-configuration"></a>强制隧道配置

为了支持强制隧道，服务管理流量将与客户流量分开。 还需要一个名为“AzureFirewallManagementSubnet”的专用子网（最小子网大小为“/26”），此子网有其自己的关联公共 IP 地址。 此子网上允许的唯一路由是到 Internet 的默认路由，并且必须禁用 BGP 路由传播。

如果你的默认路由通过 BGP 进行播发以强制将流量传输到本地，则必须在部署防火墙之前创建 AzureFirewallSubnet 和 AzureFirewallManagementSubnet，并设置一个默认路由到 Internet 且“虚拟网关路由传播”已禁用的 UDR。

在此配置中，AzureFirewallSubnet 现在可以包含到任何本地防火墙或 NVA 的路由。这样是为了处理流量，然后再将其传递到 Internet。 如果在此子网上启用了“虚拟网关路由传播”，还可以通过 BGP 将这些路由发布到 AzureFirewallSubnet。

例如，可以使用 VPN 网关作为下一跃点来创建 AzureFirewallSubnet 的默认路由，以转到本地设备。 也可启用“虚拟网关路由传播”以获取到本地网络的相应路由。

![虚拟网络网关路由传播](media/forced-tunneling/route-propagation.png)

将 Azure 防火墙配置为支持强制隧道后，便无法撤消配置。 如果删除防火墙上的所有其他 IP 配置，管理 IP 配置也会被删除，防火墙会被解除分配。 无法删除分配给管理 IP 配置的公共 IP 地址，但可以分配不同的公共 IP 地址。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)
