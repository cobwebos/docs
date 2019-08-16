---
title: 使用 ExpressRoute 将 Azure 虚拟网络连接到 CloudSimple
description: 描述如何获取 Azure 虚拟网络与 CloudSimple 环境之间的连接的对等互连信息
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536594"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 将 Azure 虚拟网络连接到 CloudSimple

可以将私有云网络扩展到 Azure 虚拟网络和 Azure 资源。 ExpressRoute 连接允许从私有云访问 Azure 订阅中运行的资源。

## <a name="request-authorization-key"></a>请求授权密钥

私有云与 Azure 虚拟网络之间的 ExpressRoute 连接需要授权密钥。 若要获取密钥, 请向提供<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持</a>的票证。  在请求中使用以下信息:

* 问题类型：**技术**
* 订阅:选择要将 CloudSimple 服务部署到其中的订阅 * *
* 服务：**VMware 解决方案 (按 CloudSimple)**
* 问题类型:**服务请求**
* 问题子类型:**Azure VNET 连接的授权密钥**
* 使用者:**Azure VNET 连接的授权密钥请求**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>获取用于 CloudSimple 连接的 Azure 虚拟网络的对等互连信息

若要设置连接, 必须在 Azure 虚拟网络和 CloudSimple 环境之间建立链接。  作为过程的一部分, 你必须提供对等线路 URI 和授权密钥。 从[CloudSimple 门户](access-cloudsimple-portal.md)获取 URI 和授权密钥。  选择侧边菜单上的 "**网络**", 然后选择 " **Azure 网络连接**"。 或在侧边菜单中选择 "**帐户**", 然后选择 " **Azure 网络连接**"。

请注意每个区域的对等线路 URI 和授权密钥的复制图标。 对于要连接的每个私有云:

* 单击 "**复制**" 以复制该 URI。 将其粘贴到可在其中添加到 Azure 门户的文件中。  
* 单击 "**复制**" 以复制授权密钥, 并将其粘贴到该文件中。

![虚拟网络连接页](media/network-virt-conn-page.png)

有关将 Azure 虚拟网络设置为 CloudSimple 链接的详细信息, 请参阅[使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络](azure-expressroute-connection.md)。
