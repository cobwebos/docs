---
title: Azure VMware 解决方案（按云简单 - 使用 ExpressRoute 的本地连接）
description: 描述如何使用来自云简单区域网络的 ExpressRoute 请求本地连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019615"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 从本地连接到云简单

如果已经从外部位置（如本地）连接到 Azure，则可以将其连接到 CloudSimple 环境。 可以通过 Azure 功能执行此操作，该功能允许两个 ExpressRoute 电路相互连接。 此方法在两个环境之间建立安全、私有、高带宽、低延迟的连接。

[![本地快速路由连接 - 全球覆盖](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>开始之前

从本地建立全局覆盖连接需要 **/29**网络地址块。  /29 地址空间用于 ExpressRoute 电路之间的传输网络。  传输网络不应与任何 Azure 虚拟网络、本地网络或云简单私有云网络重叠。

## <a name="prerequisites"></a>先决条件

* 在建立电路和云简单私有云网络之间的连接之前，需要 Azure ExpressRoute 电路。
* 用户必须具有在 ExpressRoute 电路上创建授权密钥的权限。

## <a name="scenarios"></a>方案

通过将本地网络连接到私有云网络，您可以以各种方式使用私有云，包括以下方案：

* 无需创建站点到站点 VPN 连接即可访问您的私有云网络。
* 将本地活动目录用作私有云上的标识源。
* 将本地运行的虚拟机迁移到私有云。
* 将私有云用作灾难恢复解决方案的一部分。
* 在私有云工作负载 VM 上使用本地资源。

## <a name="connecting-expressroute-circuits"></a>连接快速路由电路

要建立 ExpressRoute 连接，您必须在 ExpressRoute 电路上创建授权，并将授权信息提供给 CloudSimple。


### <a name="create-expressroute-authorization"></a>创建快速路由授权

1. 登录到 Azure 门户。  

2. 从顶部搜索栏中，搜索**ExpressRoute 电路**，然后单击 **"服务**"下的**ExpressRoute 电路**。
    [![快速路由电路](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. 选择要连接到云简单网络的 ExpressRoute 电路。

4. 在"快速路由"页上，单击 **"授权**"，输入授权的名称，然后单击"**保存**"。
    [![快速路由电路授权](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 通过单击复制图标复制资源 ID 和授权密钥。 将 ID 和密钥粘贴到文本文件中。
    [![快速路由电路授权副本](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **资源 ID**必须从 UI 复制，并且在提供```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```资源 ID 以支持时应采用该格式。

6. 使用<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持</a>要创建的连接提交票证。
    * 问题类型：**技术**
    * 订阅：**部署云简单服务的订阅**
    * 服务 **：VMware 解决方案（按云简单）**
    * 问题类型：**服务请求**
    * 问题子类型：**创建到本地的快速路由连接**
    * 提供在详细信息窗格中复制并保存的资源 ID 和授权密钥。
    * 为传输网络提供 /29 网络地址空间。
    * 您是否通过 ExpressRoute 发送默认路由？
    * 私有云流量是否应使用通过 ExpressRoute 发送的默认路由？

    > [!IMPORTANT]
    > 发送默认路由允许您使用本地互联网连接从私有云发送所有互联网流量。  要禁用在私有云上配置的默认路由并使用本地连接默认路由，请提供支持票证中的详细信息。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 网络连接的更多信息](cloudsimple-azure-network-connection.md)  
