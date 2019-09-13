---
title: Azure VMware 解决方案（通过 CloudSimple）使用 ExpressRoute 的本地连接
description: 介绍如何使用 CloudSimple 区域网络中的 ExpressRoute 请求本地连接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910547"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>使用 ExpressRoute 从本地连接到 CloudSimple

如果已具有从外部位置（如本地）到 Azure 的 Azure ExpressRoute 连接，则可以将其连接到 CloudSimple 环境。 可以通过 Azure 功能实现此操作，这种功能允许两条 ExpressRoute 线路彼此连接。 此方法在两个环境之间建立安全、专用、高带宽、低延迟连接。

[![本地 ExpressRoute 连接-Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>开始之前

从本地建立 Global Reach 连接需要一个 **/29**网络地址块。  /29 地址空间用于 ExpressRoute 线路之间的传输网络。  传输网络不应与任何 Azure 虚拟网络、本地网络或 CloudSimple 私有云网络重叠。

## <a name="prerequisites"></a>先决条件

* 需要使用 Azure ExpressRoute 线路，然后才能建立线路与 CloudSimple 私有云网络之间的连接。
* 需要用户有权在 ExpressRoute 线路上创建授权密钥。

## <a name="scenarios"></a>方案

通过将本地网络连接到私有云网络，可以通过多种方式使用私有云，包括以下方案：

* 访问私有云网络，无需创建站点到站点 VPN 连接。
* 在私有云上使用本地 Active Directory 作为标识源。
* 将本地运行的虚拟机迁移到私有云。
* 使用私有云作为灾难恢复解决方案的一部分。
* 使用私有云工作负荷 Vm 上的本地资源。

## <a name="connecting-expressroute-circuits"></a>连接 ExpressRoute 线路

若要建立 ExpressRoute 连接，必须在 ExpressRoute 线路上创建授权，并向 CloudSimple 提供授权信息。


### <a name="create-expressroute-authorization"></a>创建 ExpressRoute 授权

1. 登录到 Azure 门户。

2. 在顶部搜索栏中，搜索 " **expressroute 线路**"，然后单击 "**服务**" 下的**expressroute 线路**。
    [![ExpressRoute 线路](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. 选择要连接到 CloudSimple 网络的 ExpressRoute 线路。

4. 在 "ExpressRoute" 页上，单击 "**授权**"，为授权输入名称，然后单击 "**保存**"。
    [![ExpressRoute 线路授权](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 单击 "复制" 图标，复制 "资源 ID" 和 "授权密钥"。 将 ID 和密钥粘贴到文本文件中。
    [![ExpressRoute 线路授权复制](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > 必须从 UI 复制**资源 ID** ，并在提供支持时将其```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>```设置为格式。

6. 为要创建的连接提供<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持</a>的票证。
    * 问题类型：**技术**
    * 订阅：**部署 CloudSimple 服务的订阅**
    * 服务：**VMware 解决方案（按 CloudSimple）**
    * 问题类型：**服务请求**
    * 问题子类型：**创建到本地的 ExpressRoute 连接**
    * 提供已复制并保存在详细信息窗格中的资源 ID 和授权密钥。
    * 为传输网络提供一个/29 网络地址空间。
    * 是否通过 ExpressRoute 发送默认路由？
    * 私有云流量是否应使用通过 ExpressRoute 发送的默认路由？

    > [!IMPORTANT]
    > 通过发送默认路由，你可以使用本地 internet 连接从私有云发送所有 internet 流量。  若要禁用私有云上配置的默认路由并使用本地连接默认路由，请在支持票证中提供详细信息。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 网络连接的详细信息](cloudsimple-azure-network-connection.md)  
