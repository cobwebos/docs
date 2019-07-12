---
title: 用于管理 Azure CloudSimple VMware 解决方案的关键概念
description: 介绍用于管理 Azure CloudSimple VMware 解决方案的重要概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d87871fe8faaaab2e56d4a0426cd5e5f0899c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595617"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>用于管理 Azure CloudSimple VMware 解决方案的关键概念

管理 Azure CloudSimple VMware 解决方案，需要了解以下概念：

* 可以从 CloudSimple-服务作为 Azure VMware 解决方案显示 CloudSimple 服务
* CloudSimple 节点，由 CloudSimple-节点显示为 VMware 的 Azure 解决方案
* CloudSimple 私有云
* 服务的网络
* CloudSimple 虚拟机，这将显示为 Azure VMware 解决方案通过 CloudSimple-虚拟机

## <a name="cloudsimple-service"></a>CloudSimple 服务

使用 CloudSimple 服务时，可以创建和管理从 Azure 门户通过 CloudSimple 关联与 VMware 解决方案的所有资源。 在想要使用该服务的每个区域中创建的服务资源。

详细了解如何[CloudSimple 服务](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>CloudSimple 节点

CloudSimple 节点是在其中部署 VMware ESXi 虚拟机监控程序的专用、 祼机超聚合计算和存储主机。 此节点将它们合并到 VMware vSphere、 vCenter、 vSAN 和 NSX 平台。 此外启用 CloudSimple 网络服务和边缘网络服务。 每个节点提供服务作为一个单元可以预配创建的计算和存储容量[CloudSimple 私有云](cloudsimple-private-cloud.md)。 预配或保留 CloudSimple 服务已上市的区域中的节点。


详细了解如何[CloudSimple 节点](cloudsimple-node.md)。

## <a name="cloudsimple-private-cloud"></a>CloudSimple 私有云

CloudSimple 私有云是由自己管理域中的 vCenter 服务器管理的独立的 VMware stack 环境。 VMware 堆栈包括 ESXi 主机、 vSphere、 vCenter、 vSAN 和 NSX。 则堆栈会在专用节点 （专用的隔离的裸机硬件），并使用由用户通过本机 VMware 工具，包括 vCenter 和 NSX 管理器。 专用的节点部署在 Azure 位置和由 Azure 管理。 每个私有云可以进行分段和通过网络服务，如 Vlan 和子网和防火墙表保护。 与在本地环境和 Azure 网络连接是通过使用安全、 专用 VPN，以及 Azure ExpressRoute 创建的连接。

详细了解如何[CloudSimple 私有云](cloudsimple-private-cloud.md)。

## <a name="service-networking"></a>服务的网络

CloudSimple 服务提供了每个区域部署 CloudSimple 服务所在的网络。 网络是默认情况下启用了路由的单一 TCP 第 3 层地址空间。 所有私有云基础架构并在此区域中创建的子网相互通信而无需任何其他配置。 在 vCenter 上创建分布式的端口组，使用 Vlan。 可以使用以下网络功能来配置和在私有云中保护工作负荷的资源：

* [Vlan 和子网](cloudsimple-vlans-subnets.md)
* [防火墙表](cloudsimple-firewall-tables.md)
* [VPN 网关](cloudsimple-vpn-gateways.md)
* [公共 IP](cloudsimple-public-ip-address.md)
* [Azure 网络连接](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 虚拟机

使用 CloudSimple 服务时，可以从 Azure 门户中管理 VMware 虚拟机。 一个或多个群集或 vSphere 环境中的资源池可以映射到其创建该服务的订阅。

了解有关以下方面的详细信息：

* [CloudSimple 虚拟机](cloudsimple-virtual-machines.md)
* [Azure 订阅映射](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
