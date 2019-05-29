---
title: 使用 Azure 虚拟 WAN 创建与 Azure 和本地环境的 ExpressRoute 连接 | Microsoft Docs
description: 在本教程中，你将了解如何使用 Azure 虚拟 WAN 创建与 Azure 和本地环境的 ExpressRoute 连接。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 3e6ab347a86aa8d04c8ebd0382178b873c987300
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605431"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>教程：使用 Azure 虚拟 WAN（预览版）创建 ExpressRoute 关联

本教程介绍如何使用 ExpressRoute 线路和关联通过虚拟 WAN 连接到 Azure 中的资源。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 vWAN
> * 创建中心
> * 查找线路并将其关联到中心
> * 将线路关联到中心
> * 将 VNet 连接到中心
> * 查看虚拟 WAN
> * 查看资源运行状况
> * 监视连接

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>注册此功能

在配置虚拟 WAN 之前，必须先在预览版中注册订阅。 否则无法在门户中使用虚拟 WAN。 若要注册，请使用订阅 ID 向 **azurevirtualwan\@microsoft.com** 发送电子邮件。 注册订阅后，你会收到电子邮件。

**预览注意事项：**

必须在支持 [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported) 的国家/地区启用 ExpressRoute 线路。

## <a name="vnet"></a>1.创建虚拟网络

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2.创建虚拟 WAN

从浏览器导航到 [Azure 门户（预览版）](https://aka.ms/azurevirtualwanpreviewfeatures)并使用 Azure 帐户登录。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>“入门”页

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3.创建中心

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4.查找线路并将其关联到中心

1. 选择 vWAN，在“虚拟 WAN 体系结构”  下，选择“ExpressRoute 线路”  。
1. 如果 ExpressRoute 线路与 vWAN 在同一订阅中，请在订阅中单击“选择 ExpressRoute 线路”  。 
1. 使用下拉菜单，选择要关联到中心的 ExpressRoute。
1. 如果 ExpressRoute 线路不在同一订阅中，或者你已获得[授权密钥和对等 ID](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)，请选择“查找兑换授权密钥的线路” 
1. 输入以下详细信息：
1. **授权密钥** - 如上所述，由线路所有者生成
1. **对等线路 URI** - 线路所有者提供的线路 URI，是线路的唯一标识符
1. **路由权重** - [路由权重](../expressroute/expressroute-optimize-routing.md) - 当来自不同对等互连位置的多个线路连接到同一个中心时，允许你选择某些路径
1. 单击“查找线路”  并选择线路（如果找到）。
1. 从下拉列表中选择一个或多个中心，然后单击“保存”  。

## <a name="vnet"></a>5.将 VNet 连接到中心

此步骤在中心与 VNet 之间创建对等互连。 针对要连接的每个 VNet 重复这些步骤。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。 
2. 在虚拟网络连接页上，单击“+添加连接”。 
3. 在“添加连接”页上填写以下字段  ：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。


## <a name="viewwan"></a>6.查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>7.查看资源运行状况

1. 导航到 WAN。
2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。  

## <a name="connectmon"></a>8.监视连接

创建一个连接，用于监视 Azure VM 与远程站点之间的通信。 有关如何设置连接监视器的信息，请参阅[监视网络通信](~/articles/network-watcher/connection-monitor.md)。 源字段是 Azure 中的 VM IP，目标 IP 是站点 IP。

## <a name="cleanup"></a>9.清理资源

不再需要这些资源时，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 vWAN
> * 创建中心
> * 查找线路并将其关联到中心
> * 将线路关联到中心
> * 将 VNet 连接到中心
> * 查看虚拟 WAN
> * 查看资源运行状况
> * 监视连接

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
