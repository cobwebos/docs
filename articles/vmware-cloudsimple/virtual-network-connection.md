---
title: 使用 ExpressRoute 将 Azure 虚拟网络连接到云简单 - 按云简单方式连接 Azure VMware 解决方案
description: 描述如何获取 Azure 虚拟网络和 CloudSimple 环境之间连接的对等信息
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563994"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>使用快速路由将 Azure 虚拟网络连接到云简单

您可以将私有云网络扩展到 Azure 虚拟网络和 Azure 资源。 通过 ExpressRoute 连接，您可以从私有云访问 Azure 订阅中运行的资源。

## <a name="request-authorization-key"></a>请求授权密钥

私有云和 Azure 虚拟网络之间的 ExpressRoute 连接需要授权密钥。 要获取密钥，使用<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持</a>文件票证。  在请求中使用以下信息：

* 问题类型：**技术**
* 订阅：**选择部署云简单服务的订阅**
* 服务 **：VMware 解决方案（按云简单）**
* 问题类型：**服务请求**
* 问题子类型 **：Azure VNET 连接的授权密钥**
* 主题 **：Azure VNET 连接的授权密钥请求**

## <a name="get-peering-information-from-cloudsimple-portal"></a>从云简单门户获取对等信息

要设置连接，必须在 Azure 虚拟网络和 CloudSimple 环境之间建立连接。  作为该过程的一部分，您必须提供对等电路 URI 和授权密钥。 从[CloudSimple 门户](access-cloudsimple-portal.md)获取 URI 和授权密钥。  选择**侧**菜单上的网络，然后选择 Azure**网络连接**。 或者选择侧菜单上的**帐户**，然后选择**Azure 网络连接**。

使用*复制*图标复制对等电路 URI 以及每个区域的授权密钥。 对于要连接的每个云简单区域：

1. 单击 **"复制**"以复制 URI。 将其粘贴到可用于添加到 Azure 门户的文件中。  
2. 单击 **"复制**"以复制授权密钥并将其粘贴到文件中。

复制处于**可用**状态的授权密钥和对等电路 URI。  **已使用**状态表示密钥已用于创建虚拟网络连接。

![虚拟网络连接页面](media/virtual-network-connection.png)

有关将 Azure 虚拟网络设置为云简单链接的详细信息，请参阅[使用 ExpressRoute 将云简单私有云环境连接到 Azure 虚拟网络](azure-expressroute-connection.md)。

## <a name="next-steps"></a>后续步骤

* [Azure 虚拟网络连接到私有云](azure-expressroute-connection.md)
* [使用 Azure 快速路由连接到本地网络](on-premises-connection.md)
