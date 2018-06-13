---
title: 适用于容器的 Azure 虚拟网络 | Microsoft Docs
description: 了解适用于 Kubernetes 群集的 CNI 插件，该插件使容器能够在彼此之间以及与虚拟网络中的其他资源进行通信。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2017
ms.locfileid: "26766392"
---
# <a name="container-networking"></a>容器网络

Azure 提供了一个[容器网络接口 (CNI) 插件](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md)，可通过该插件使用本机 Azure 网络功能部署并管理你自己的 Kubernetes 群集。 将 Kubernetes 群集与 [Azure 容器服务引擎](https://github.com/Azure/acs-engine)（或称为 ACS 引擎）部署在一起时会默认启用此插件。

## <a name="networking-capabilities"></a>网络功能

容器可以利用虚拟网络提供的丰富的一组功能：
-   可以为群集创建单独的虚拟网络，也可以将群集部署到现有的虚拟网络中。 
-   群集中的每个 Pod 都从虚拟网络中接收 IP 地址，并且可以直接与群集中的其他 Pod 以及虚拟网络中的任何虚拟机进行通信。 
-   Pod 可以通过 ExpressRoute 和站点到站点 VPN 连接来连接到其他 Pod 和对等互连虚拟网络中的虚拟机，以及连接到本地网络。 本地资源可以与 Pod 进行通信。 
-   可以通过 Azure 负载均衡器向 Internet 公开 Kubernetes 服务。  
-   启用了服务终结点的子网中的 Pod 可以安全地连接到 Azure 服务（例如，存储和 SQL 数据库）。
-   可以通过用户定义的路由将来自 Pod 的流量路由到网络虚拟设备。 
-   Pod 可以访问 Internet 上的公共资源。
-   可以为 Pod 分配公共 IP 地址，该地址可以与 DNS 名称相关联。
 
## <a name="limits"></a>限制
使用此插件时，可以在一个 Kubernetes 群集中部署最多 4,000 个节点，在每个节点中部署最多 250 个 Pod，总限制为每个群集 16,000 个 Pod。

## <a name="constraints"></a>约束
- 将 Kubernetes 群集与 [Azure 容器服务 (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 部署在一起时或将 [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 群集与 Kubernetes 部署在一起时不用启用此插件。
- 没有提供对 Kubernetes 网络策略（包括 DNS 或访问策略）的本机支持。
- 此插件不支持以 Pod 为单位的网络策略。

## <a name="pricing"></a>定价
可以免费使用 CNI 插件。

## <a name="next-steps"></a>后续步骤

了解如何在[自己的虚拟网络](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni)中[部署 Kubernetes 群集](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md)。
