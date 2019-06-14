---
title: VMware CloudSimple-Azure 网络连接解决方案
description: 了解如何在 Azure 虚拟网络连接到 CloudSimple 区域网络
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497641"
---
# <a name="azure-network-connections-overview"></a>Azure 网络连接概述

在区域中，创建 CloudSimple 服务时它：

* 创建 Azure ExpressRoute 线路，并将其附加到该区域中的服务
* 允许从 CloudSimple 区域网络连接到 Azure 虚拟网络或使用 Azure ExpressRoute 的本地网络
* 提供可在你的 Azure 订阅或你的本地网络，从你的私有云环境中运行的访问服务

连接是指：

* 安全
* 专用
* 高带宽
* 低延迟

## <a name="benefits"></a>优点

Azure 网络连接，您可以：

* 使用 Azure 为您的私有云上的虚拟机的备份目标。
* 部署用于加密私有云 vSAN 数据存储在 Azure 订阅中的 KMS 服务器。
* 使用了当应用程序在公有云中运行的应用程序的 web 层和数据库层运行在您的私有云的混合应用程序。

## <a name="azure-virtual-network-connection"></a>Azure 虚拟网络连接

私有云可以连接到 Azure 资源使用 ExpressRoute。  此连接可用于访问不同 Azure 订阅中运行从您的私有云的资源。  此连接使你能够扩展你的私有云网络到 Azure 虚拟网络。

![Azure ExpressRoute 连接到虚拟网络](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute 连接到本地网络

你可以连接到 CloudSimple 区域的现有 Azure ExpressRoute 线路。 ExpressRoute 全球覆盖功能用于与每个其他连接两个线路。  在本地和 CloudSimple ExpressRoute 线路之间建立的连接。  此连接使你能够扩展你的本地网络到私有云网络。

![在本地 ExpressRoute 连接的全球性覆盖](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>后续步骤

* [获取对等互连 CloudSimple 连接到 Azure 虚拟网络的信息](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [从本地连接到 CloudSimple 使用 ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
