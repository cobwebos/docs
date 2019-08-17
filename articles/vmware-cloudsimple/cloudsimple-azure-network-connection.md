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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563163"
---
# <a name="azure-network-connections-overview"></a>Azure 网络连接概述

在区域中创建 CloudSimple 服务时, 该服务会:

* 创建 Azure ExpressRoute 线路, 并将其附加到该区域中的服务。
* 使用 Azure ExpressRoute 将 CloudSimple 区域网络连接到 Azure 虚拟网络或本地网络。
* 提供对你的 Azure 订阅中运行的服务或你的私有云环境中的本地网络的访问。

ExpressRoute 连接的带宽较低, 延迟较低。

## <a name="benefits"></a>优点

Azure 网络连接允许:

* 使用 Azure 作为私有云上的虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器, 以加密私有云 vSAN 数据存储。
* 使用混合应用程序, 在应用程序和数据库层在私有云中运行时, 应用程序的 web 层在公有云中运行。

## <a name="azure-virtual-network-connection"></a>Azure 虚拟网络连接

私有云可以使用 ExpressRoute 连接到 Azure 资源。  ExpressRoute 连接允许从私有云访问 Azure 订阅中运行的资源。  此连接可让你将私有云网络扩展到 Azure 虚拟网络。

[![Azure ExpressRoute 与虚拟网络的连接](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>到本地网络的 ExpressRoute 连接

可以将现有的 Azure ExpressRoute 线路连接到 CloudSimple 区域。 ExpressRoute Global Reach 功能用于将两个线路彼此连接起来。  在本地和 CloudSimple ExpressRoute 线路之间建立连接。  此连接可让你将本地网络扩展到私有云网络。

![本地 ExpressRoute 连接-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>后续步骤

* [使用 ExpressRoute 将 Azure 虚拟网络连接到 CloudSimple](virtual-network-connection.md)
* [使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)
