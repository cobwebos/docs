---
title: 将 Azure 应用程序网关与 VMware 虚拟机一起使用
description: 描述如何使用 Azure 应用程序网关管理 VMware 虚拟机中运行的 Web 服务器的传入 Web 流量，以赢得云简单私有云环境
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015450"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>在云简单私有云环境中将 Azure 应用程序网关与 VMware 虚拟机一起使用

您可以使用 Azure 应用程序网关管理云简单私有云环境中 VMware 虚拟机中运行的 Web 服务器传入 Web 流量。

通过在公私混合部署中利用 Azure 应用程序网关，可以管理应用程序的 Web 流量、提供安全前端和卸载 SSL 处理，以便其服务在 VMware 环境中运行。 Azure 应用程序网关根据配置的规则和运行状况探测将传入 Web 流量路由到驻留在 VMware 环境中的后端池实例。

此 Azure 应用程序网关解决方案要求您：

* 一个 Azure 订阅。
* 创建和配置 Azure 虚拟网络和虚拟网络中的子网。
* 创建和配置 NSG 规则，并使用 ExpressRoute 与云简单私有云对等 vNet。
* 创建&配置私有云。
* 创建&配置 Azure 应用程序网关。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 应用程序网关部署方案

在这种情况下，Azure 应用程序网关在 Azure 虚拟网络中运行。 虚拟网络通过 ExpressRoute 电路连接到您的私有云。 私有云中的所有子网都是从虚拟网络子网可达到 IP 的。

![Azure 虚拟网络中的 Azure 负载均衡器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

部署过程由以下任务组成：

1. [验证是否满足先决条件](#1-verify-prerequisites)
2. [将 Azure 虚拟连接连接到私有云](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [部署 Azure 应用程序网关](#3-deploy-an-azure-application-gateway)
4. [在私有云中创建和配置 Web 服务器 VM 池](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 验证先决条件

验证是否满足这些先决条件：

* 已创建 Azure 资源管理器和虚拟网络。
* 已在 Azure 虚拟网络中创建专用子网（用于应用程序网关）。
* 云简单私有云已创建。
* 虚拟网络中的 IP 子网与私有云中的子网之间没有 IP 冲突。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. 将 Azure 虚拟网络连接到私有云

要将 Azure 虚拟网络连接到私有云，请按照此过程操作。

1. [在云简单门户中，复制 ExpressRoute 对等信息](virtual-network-connection.md)。

2. [为 Azure 虚拟网络配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. [将虚拟网络链接到云简单快速路由电路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用复制的对等信息将虚拟网络链接到 ExpressRoute 电路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3. 部署 Azure 应用程序网关

有关此的详细信息，可在使用 Azure[门户使用基于路径的路由规则创建应用程序网关](../application-gateway/create-url-route-portal.md)中。 以下是所需步骤的摘要：

1. 在订阅和资源组中创建虚拟网络。
2. 在虚拟网络中创建子网（用作专用子网）。
3. 创建标准应用程序网关（可选地启用 WAF）：从 Azure 门户主页，从页面左上角单击**资源** > **网络** > **应用程序网关**。 选择标准 SKU 和大小并提供 Azure 订阅、资源组和位置信息。 如果需要，请为此应用程序网关创建新的公共 IP，并提供虚拟网络和应用程序网关专用子网的详细信息。
4. 使用虚拟机添加后端池并将其添加到应用程序网关。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 在私有云中创建和配置 Web 服务器 VM 池

在 vCenter 中，使用您选择的操作系统和 Web 服务器（如 Windows/IIS 或 Linux/Apache）创建 VM。 选择为私有云中的 Web 层指定的子网/VLAN。 验证 Web 服务器 VM 的至少一个 vNIC 是否位于 Web 层子网中。
