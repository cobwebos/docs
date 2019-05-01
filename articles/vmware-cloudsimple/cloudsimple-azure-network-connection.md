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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576995"
---
# <a name="azure-network-connection-overview"></a>Azure 网络连接概述

在区域中，创建 CloudSimple 服务时它：

* 创建 Azure ExpressRoute 线路，并将其附加到该区域中的服务
* 连接到 Azure 虚拟网络或使用 Azure ExpressRoute 的本地网络 CloudSimple 区域网络
* 提供可在你的 Azure 订阅或你的本地网络，从你的私有云环境中运行的访问服务

此连接是高带宽较低的延迟。

## <a name="benefits"></a>优点

Azure 网络连接，您可以：

* 使用 Azure 为您的私有云上的虚拟机的备份目标。
* 部署用于加密私有云 vSAN 数据存储在 Azure 订阅中的 KMS 服务器。
* 使用了当应用程序在公有云中运行的应用程序的 web 层和数据库层运行在您的私有云的混合应用程序。

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute 连接到本地网络

你可以连接到 CloudSimple 区域的现有 Azure ExpressRoute 线路。 ExpressRoute 全球覆盖功能用于与每个其他连接两个线路。  在本地和 CloudSimple ExpressRoute 线路之间建立的连接。

此方法之间建立连接是在两种环境：

* 安全
* 专用
* 高带宽
* 低延迟

若要创建 ExpressRoute 连接到本地网络[请联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

* [设置虚拟网络连接](https://docs.azure.cloudsimple.com/virtual-network-connection)