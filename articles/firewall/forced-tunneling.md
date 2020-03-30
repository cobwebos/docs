---
title: Azure 防火墙强制隧道
description: 您可以配置强制隧道，将 Internet 绑定流量路由到其他防火墙或网络虚拟设备，以便进一步处理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597264"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure 防火墙强制隧道（预览）

您可以配置 Azure 防火墙，将所有 Internet 绑定流量路由到指定的下一个跃点，而不是直接转到 Internet。 例如，您可能有一个本地边缘防火墙或其他网络虚拟设备 （NVA） 来处理网络流量，然后再将其传递到 Internet。

> [!IMPORTANT]
> Azure 防火墙强制隧道当前处于公共预览版中。
>
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认情况下，Azure 防火墙上不允许强制隧道化，以确保满足其所有出站 Azure 依赖项。 禁用*Azure 防火墙子网*上默认路由不直接转到 Internet 的用户定义路由 （UDR） 配置。

## <a name="forced-tunneling-configuration"></a>强制隧道配置

为了支持强制隧道，服务管理流量与客户流量分离。 使用其关联的公共 IP 地址，需要一个名为*Azure 防火墙管理子网*（最小子网大小 /26）的其他专用子网。 此子网上允许的唯一路由是到 Internet 的默认路由，并且必须禁用 BGP 路由传播。

如果通过 BGP 通告的默认路由以强制流量到本地，则必须在部署防火墙之前创建*Azure 防火墙子网*和*Azure 防火墙管理子网*，并且具有默认路由到 Internet 的 UDR 和禁用**虚拟网络网关路由传播**。

在此配置中 *，Azure 防火墙 Subnet*现在可以包括路由到任何本地防火墙或 NVA，用于在流量传递到 Internet 之前处理流量。 如果在此子网上启用**了虚拟网络网关路由传播**，还可以通过 BGP 将这些路由发布到*Azure 防火墙子网*。

例如，您可以在*Azure 防火墙 Subnet*上创建默认路由，将 VPN 网关作为下一个要跳以到达本地设备。 或者，您可以启用**虚拟网络网关路由传播**，以获取到本地网络的适当路由。

![虚拟网络网关路由传播](media/forced-tunneling/route-propagation.png)

将 Azure 防火墙配置为支持强制隧道化后，无法撤消配置。 如果删除防火墙上的所有其他 IP 配置，管理 IP 配置也被删除，防火墙也已处理。 无法删除分配给管理 IP 配置的公共 IP 地址，但您可以分配其他公共 IP 地址。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)