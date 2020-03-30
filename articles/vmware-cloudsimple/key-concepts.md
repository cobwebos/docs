---
title: 通过云简单管理 Azure VMware 解决方案的关键概念
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述按云简单管理 Azure VMware 解决方案的关键概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564613"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>通过云简单管理 Azure VMware 解决方案的关键概念

通过云简单管理 Azure VMware 解决方案需要了解以下概念：

* 云简单服务，按云简单显示为 Azure VMware 解决方案 - 服务
* 云简单节点，按云简单显示为 Azure VMware 解决方案 - 节点
* 云简单私有云
* 服务网络
* 云简单虚拟机，它显示为 Azure VMware 解决方案（由云简单 - 虚拟机）

## <a name="cloudsimple-service"></a>云简单服务

借助 CloudSimple 服务，您可以通过云简单创建和管理与 VMware 解决方案关联的所有资源。这些资源来自 Azure 门户。 在要使用该服务的每个区域创建服务资源。

了解有关[云简单服务的更多详细信息](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>云简单节点

CloudSimple 节点是一个专用的裸机超融合计算和存储主机，其中部署了 VMware ESXi 虚拟机管理程序。 然后，此节点将合并到 VMware vSphere、vCenter、vSAN 和 NSX 平台中。 云简单网络服务和边缘网络服务也已启用。 每个节点都用作计算和存储容量的单元，您可以预配这些单元来创建[云简单私有云](cloudsimple-private-cloud.md)。 在云简单服务可用的区域中预配或保留节点。

了解有关[云简单节点](cloudsimple-node.md)的更多。

## <a name="cloudsimple-private-cloud"></a>云简单私有云

CloudSimple 私有云是由 vCenter 服务器在其自己的管理域中管理的隔离 VMware 堆栈环境。 VMware 堆栈包括 ESXi 主机、vSphere、vCenter、vSAN 和 NSX。 堆栈在专用节点（专用和隔离裸机硬件）上运行，用户通过包括 vCenter 和 NSX 管理器的本机 VMware 工具使用。 专用节点部署在 Azure 位置，并由 Azure 管理。 通过使用网络服务（如 VLAN、子网和防火墙表）可以对每个私有云进行分段和保护。 使用安全专用 VPN 和 Azure 快速路由连接创建到本地环境和 Azure 网络的连接。

了解有关[云简单私有云的更多。](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>服务网络

CloudSimple 服务提供部署云简单服务的每个区域的网络。 网络是单个 TCP 第 3 层地址空间，默认情况下启用路由。 在此区域中创建的所有私有云和子网相互通信，无需任何其他配置。 通过使用 VLAN 在 vCenter 上创建分布式端口组。 您可以使用以下网络功能在私有云中配置和保护工作负载资源：

* [VLAN 和子网](cloudsimple-vlans-subnets.md)
* [防火墙表](cloudsimple-firewall-tables.md)
* [VPN 网关](cloudsimple-vpn-gateways.md)
* [公共知识产权](cloudsimple-public-ip-address.md)
* [Azure 网络连接](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>云简单虚拟机

使用 CloudSimple 服务，可以从 Azure 门户管理 VMware 虚拟机。 可以从 vSphere 环境中的一个或多个群集或资源池映射到创建服务的订阅。

了解有关以下方面的详细信息：

* [云简单虚拟机](cloudsimple-virtual-machines.md)
* [Azure 订阅映射](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
