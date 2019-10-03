---
title: 将 Azure 应用程序网关与 VMware 虚拟机配合使用
description: 介绍如何使用 Azure 应用程序网关来管理在 VMware 虚拟机中运行的 web 服务器的传入 web 流量 win the CloudSimple 私有云环境
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576779"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>在 CloudSimple 私有云环境中将 Azure 应用程序网关与 VMware 虚拟机配合使用

你可以使用 Azure 应用程序的网关来管理你的 web 服务器的传入 web 流量, 该流量适用于你的 CloudSimple 私有云环境中的 VMware 虚拟机。

通过利用公有专用混合部署中的 Azure 应用程序网关, 你可以管理应用程序的 web 流量, 为其在 VMware 环境中运行的服务提供安全前端和卸载 SSL 处理。 Azure 应用程序网关根据配置的规则和运行状况探测, 将传入的 web 流量路由到位于 VMware 环境中的后端池实例。

此 Azure 应用程序网关解决方案要求您:

* 一个 Azure 订阅。
* 在虚拟网络中创建和配置 Azure 虚拟网络和子网。
* 使用 ExpressRoute 为 CloudSimple 私有云创建和配置 NSG 规则并对其进行配置。
* 创建 & 配置私有云。
* 创建 & 配置 Azure 应用程序网关。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 应用程序网关部署方案

在此方案中, Azure 应用程序网关在 Azure 虚拟网络中运行。 虚拟网络通过 ExpressRoute 线路连接到私有云。 私有云中的所有子网都是从虚拟网络子网访问 IP 的 IP。

![Azure 虚拟网络中的 azure 负载均衡器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

部署过程包括以下任务:

1. [验证是否满足先决条件](#1-verify-prerequisites)
2. [将 Azure 虚拟连接连接到私有云](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [部署 Azure 应用程序网关](#3-deploy-an-azure-application-gateway)
4. [在私有云中创建和配置 Web 服务器 VM 池](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1.验证先决条件

验证是否满足以下先决条件:

* 已创建 Azure 资源管理器和虚拟网络。
* 已创建 Azure 虚拟网络中的专用子网 (适用于应用程序网关)。
* 已创建 CloudSimple 私有云。
* 虚拟网络中的 IP 子网和私有云中的子网之间没有 IP 冲突。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2.将 Azure 虚拟网络连接到私有云

若要将 Azure 虚拟网络连接到私有云, 请按照此过程操作。

1. [在 CloudSimple 门户中, 复制 ExpressRoute 对等互连信息](virtual-network-connection.md)。

2. [为 Azure 虚拟网络配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. 将[虚拟网络链接到 CloudSimple ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用复制的对等互连信息将虚拟网络链接到 ExpressRoute 线路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3.部署 Azure 应用程序网关

有关此操作的详细说明, 请访问[使用 Azure 门户通过基于路径的路由规则创建应用程序网关](../application-gateway/create-url-route-portal.md)。 下面是所需步骤的摘要:

1. 在订阅和资源组中创建虚拟网络。
2. 在虚拟网络中创建一个子网 (用作专用子网)。
3. 创建标准应用程序网关 (可选):从 "Azure 门户" 主页上, 单击页面左上角的 "**资源** > " "**网络** > " "**应用程序网关**"。 选择标准 SKU 和大小, 并提供 Azure 订阅、资源组和位置信息。 如果需要, 请为此应用程序网关创建新的公共 IP, 并提供有关该应用程序网关的虚拟网络和专用子网的详细信息。
4. 添加包含虚拟机的后端池, 并将其添加到应用程序网关。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4.在私有云中创建和配置 web 服务器 VM 池

在 vCenter 中, 创建具有所选 OS 和 web 服务器的 Vm (如 Windows/IIS 或 Linux/Apache)。 选择为私有云中的 web 层指定的子网/VLAN。 验证 web 服务器虚拟机上至少有一个 vNIC 位于 web 层子网中。
