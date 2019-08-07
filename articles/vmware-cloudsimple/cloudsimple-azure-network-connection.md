---
title: 通过 CloudSimple 的 VMware 解决方案-Azure 网络连接
description: 了解如何将 Azure 虚拟网络连接到 CloudSimple 区域网络
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812710"
---
# <a name="azure-network-connections-overview"></a>Azure 网络连接概述

在区域中创建 CloudSimple 服务时, 该服务会:

* 创建 Azure ExpressRoute 线路, 并将其附加到该区域中的服务
* 允许使用 Azure ExpressRoute 从 CloudSimple 区域网络连接到 Azure 虚拟网络或本地网络
* 在你的私有云环境中提供在你的 Azure 订阅或本地网络中运行的访问服务

连接为:

* 安全
* 专用
* 高带宽
* 低延迟

## <a name="benefits"></a>优点

Azure 网络连接允许:

* 使用 Azure 作为私有云上的虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器, 以加密私有云 vSAN 数据存储。
* 使用混合应用程序, 在应用程序和数据库层在私有云中运行时, 应用程序的 web 层在公有云中运行。

## <a name="azure-virtual-network-connection"></a>Azure 虚拟网络连接

私有云可以使用 ExpressRoute 连接到 Azure 资源。  可以使用此连接从私有云访问 Azure 订阅中运行的不同资源。  此连接可让你将私有云网络扩展到 Azure 虚拟网络。

![Azure ExpressRoute 与虚拟网络的连接](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>到本地网络的 ExpressRoute 连接

可以将现有的 Azure ExpressRoute 线路连接到 CloudSimple 区域。 ExpressRoute Global Reach 功能用于将两个线路彼此连接起来。  在本地和 CloudSimple ExpressRoute 线路之间建立连接。  此连接可让你将本地网络扩展到私有云网络。

![本地 ExpressRoute 连接-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>后续步骤

* [获取用于 CloudSimple 连接的 Azure 虚拟网络的对等互连信息](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [使用 ExpressRoute 从本地连接到 CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection)
