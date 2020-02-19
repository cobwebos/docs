---
title: Azure 防火墙强制隧道
description: 可以配置强制隧道将 Internet 绑定的流量路由到其他防火墙或网络虚拟设备，以便进一步处理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 4093f91e55272a32ce7df4a78e2ee8b3ebed5fde
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444467"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 防火墙强制隧道（预览）

可以配置 Azure 防火墙，将所有 Internet 绑定的流量路由到指定的下一跃点，而不是直接转到 Internet。 例如，你可能有本地边缘防火墙或其他网络虚拟设备（NVA）来处理网络流量，然后将其传递到 Internet。

> [!IMPORTANT]
> Azure 防火墙强制隧道目前为公共预览版。
>
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认情况下，Azure 防火墙不允许强制隧道，以确保满足所有的出站 Azure 依赖关系。 禁用了不直接连接到 Internet 的默认路由的*AzureFirewallSubnet*上的用户定义路由（UDR）配置。

## <a name="forced-tunneling-configuration"></a>强制隧道配置

为了支持强制隧道，服务管理流量与客户流量分开。 需要一个名为*AzureFirewallManagementSubnet*的额外专用子网，其中包含其自身的关联公共 IP 地址。 此子网上允许的唯一路由是到 Internet 的默认路由，并且必须禁用 BGP 路由传播。

如果有通过 BGP 播发的默认路由来强制向本地通信，则必须先创建*AzureFirewallSubnet*和*AzureFirewallManagementSubnet* ，然后再部署防火墙，并将 UDR 与 Internet 建立默认路由，并禁用虚拟网络网关路由传播。

在此配置中， *AzureFirewallSubnet*现在可以包含到任何本地防火墙或 NVA 的路由，以便在将流量传递到 Internet 之前处理流量。 如果在此子网上启用了虚拟网络网关路由传播，还可以通过 BGP 将这些路由发布到*AzureFirewallSubnet* 。

一旦将 Azure 防火墙配置为支持强制隧道，就不能撤消配置了。 如果删除防火墙上的所有其他 IP 配置，也会删除管理 IP 配置，并解除防火墙的分配。 无法删除分配给管理 IP 配置的公共 IP 地址，但可以分配不同的公共 IP 地址。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)