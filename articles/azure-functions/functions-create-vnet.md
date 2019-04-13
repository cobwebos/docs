---
title: 将 Azure Functions 与 Azure 虚拟网络集成
description: 该分布教程演示如何将 Function 连接到 Azure 虚拟网络
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523726"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>将函数应用与 Azure 虚拟网络集成

该分布教程展示如何使用 Azure Functions 连接 Azure VNET 中的资源。

本教程中我们将部署在专用非 internet 可访问，VNET 中的 VM 上的 WordPress 网站。 然后，部署一个可访问 Internet 和 VNET 的 Function。 我们将使用该函数能够从 VNET 内部部署的 WordPress 站点访问资源。

若要深入了解系统的工作原理、故障排除和高级配置，请参阅文档[将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。 高级版计划中的 azure 函数具有与 web 应用相同的托管功能，因此所有的功能和该文档中的限制适用于函数以及。

## <a name="topology"></a>拓扑

 ![VNet 集成 UI][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>在 VNET 中创建 VM

首先，在 VNET 中创建运行 Wordpress 的预配置 VM。 

选择 VM 上的 Wordpress 是因为它是可以在 VNET 内部署的最便宜的资源之一。 请注意，这种情况下还可以使用 VNET，例如 REST Api、 应用服务环境和其他 Azure 服务中的任何资源。

1. 转到 Azure 门户
2. 打开“创建资源”边栏选项卡，添加新资源
3. 搜索"[CentOS 上的 WordPress LEMP7 最大性能](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)"并打开其创建边栏选项卡 
4. 在创建边栏选项卡中，使用以下信息配置 VM：
    1. 为此 VM 创建新的资源组，以便在本教程结束时更轻松地清理资源。 将我的资源组命名为“Function-VNET-教程”
    1. 为虚拟机提供唯一名称。 将其命名为“VNET-Wordpress”
    1. 选择离你最近的区域
    1. 选择大小为 B1s（1 vcpu、1 GB 内存）
    1. 对于管理员帐户，选择密码验证并输入惟一的用户名和密码。 在本教程中，除非需要进行故障排除，否则不需要登录 VM。
    
        ![创建虚拟机基础知识选项卡](./media/functions-create-vnet/create-vm-1.png)

1. 移动到网络选项卡并输入以下信息：
    1.  创建新的虚拟网络
    1.  输入所需专用地址范围和该地址范围内的子网。 子网大小将决定可在应用服务计划中使用的 VM 数量。 如果不熟悉 IP 地址和子网设置，请参阅[涵盖这些基础知识的文档](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 IP 地址和子网设置在此方案中非常重要，因此建议先阅读一些文章并观看在线视频，理解这些内容。 
        1. 在此示例中，选择使用 10.10.0.0/16 网络，子网为 10.10.1.0/24。 选择过度预配并使用 /16 子网，因为这样更容易计算 10.10.0.0/16 网络中可用的子网。
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. 回到“网络”选项卡，将公共 IP 设置为“无”。 这将部署只可访问 VNET 的 VM。
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. 创建 VM。 此过程花费的时间约为 5 分钟。
8. 创建 VM 后，访问其“网络”选项卡并记录专用 IP 地址以供之后使用。 该 VM 不应有公共 IP。

    ![14]

现在即可拥有完全部署在虚拟网络中的 Wordpress 站点。 本站点不能从公共 Internet 访问。

## <a name="create-a-premium-plan-function-app"></a>创建 Function App 的高级计划

下一步是在高级版计划中创建函数应用。 高级计划是对新产品/服务将使用的所有专用应用服务计划权益的无服务器缩放。 消耗计划函数应用不支持 VNet 集成。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>将 Function App 连接到 VNET

与承载在 VNET 中的文件的 WordPress 站点，现在可以到 VNET 连接的函数应用。

1.  在上一步骤的 Function App 门户中，选择“平台功能”，然后选择“网络”

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  在“VNet 集成”下选择“单击进行配置”

    ![配置网络功能状态](./media/functions-create-vnet/Networking-1.png)

1. 在 VNET 集成页上，选择“添加 VNet (预览)”

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  为 Function 和应用服务计划创建要用的新子网。 请注意，子网大小将限制可添加到应用服务计划的 VM 总数。 VNET 将自动在 VNET 中的子网之间路由流量，因此 Function 与 VM 位于不同子网并不重要。 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>创建访问 VNET 中的资源的 Function

现在，Function App 可以通过 Wordpress 站点访问 VNET，所以我们将使用 Function 访问该文件并将其返回给用户。 在此示例中，我们将使用 Wordpress 站点作为 API，Function 代理作为调用 Functions（因为它们都易于设置和可视化）。 可以轻松地使用部署在 VNET 中的任何其他 API，并使用另一个 Function 通过代码调用部署在 VNET 中该 API。 部署在 VNET 中的 SQL 服务器就是很好的示例。

1. 在门户中，打开上一步骤中的 Function App
1. 选择“代理” > “+”，创建代理

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. 配置代理名称和路由。 选择 /plant 作为路由。
1. 填写前面创建的 Wordpress 站点的 IP，并将后端 URL 设置为 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

现在，如果尝试通过将后端 URL 粘贴到新的浏览器选项卡来直接访问它，则页面将超时。这种情况在意料之中，因为 Wordpress 站点只连接到 VNET，而未连接到 Internet。 如果将代理 URL 粘贴到浏览器中，可看到漂亮的植物图片，这是从 VNET 的 Wordpress 站点中获取的。 

将 Function App 连接到 Internet 和 VNET。 代理通过公共 Internet 接收请求，然后作为简单的 HTTP 代理将该请求转发到虚拟网络。 随后，代理通过公共 Internet 将响应转回给你。 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>后续步骤

在高级版计划中运行的函数共享相同基础应用服务基础结构，因为 PV2 上的 Web 应用计划。 这意味着，所有的 Web 应用文档适用于高级计划函数。

1. [了解有关此处的函数中的网络选项的详细信息](./functions-networking-options.md)
1. [读取网络常见问题此处函数](./functions-networking-faq.md)
1. [深入了解 Azure 中的 VNET](../virtual-network/virtual-networks-overview.md)
1. [启用网络功能和使用应用服务环境的控制](../app-service/environment/intro.md)
1. [使用混合连接连接到单独的本地资源而无需更改防火墙](../app-service/app-service-hybrid-connections.md)
1. [了解有关 Function 代理的详细信息](./functions-proxies.md)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
