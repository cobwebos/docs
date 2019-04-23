---
title: 将 Azure Functions 与 Azure 虚拟网络集成
description: 分步教程演示如何将函数连接到 Azure 虚拟网络
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002763"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>将函数应用与 Azure 虚拟网络集成

本教程演示如何使用 Azure Functions 连接到 Azure 虚拟网络中的资源。

对于本教程，我们将部署在不能从 internet 访问的虚拟网络中的 VM 上的 WordPress 网站。 然后，我们将对 internet 和虚拟网络部署具有访问权限的函数。 我们将使用该函数从虚拟网络内部署的 WordPress 站点访问的资源。

有关系统的工作原理的详细信息，故障排除和高级的配置，请参阅[将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。 高级版计划中的 azure 函数具有与 web 应用相同的托管功能，因此所有的功能和限制该文章中的适用于函数。

## <a name="topology"></a>拓扑

 ![虚拟网络集成的用户界面][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>创建虚拟网络中的 VM

若要开始，我们将创建虚拟网络中运行 WordPress 的预配置的 VM。 

我们选择了 VM 上的 WordPress，因为它是一个可以部署在虚拟网络的开销最少资源。 请注意，这种情况下还可以使用虚拟网络，如 REST Api、 应用服务环境和其他 Azure 服务中的任何资源。

1. 转到 Azure 门户。
2. 通过打开添加新的资源**创建资源**边栏选项卡。
3. 搜索"[CentOS 上的 WordPress LEMP7 最大性能](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)"并打开其创建边栏选项卡。 
4. 上**基础知识**选项卡上，将 VM 配置的以下信息：
    1. 为此 VM 创建新的资源组，以便在本教程结束时更轻松地清理资源。 在这里，我们将使用"函数-VNET-Tutorial"作为示例。
    1. 为虚拟机提供唯一名称。 我们使用"VNET Wordpress"作为示例。
    1. 选择离你最近的区域。
    1. B1s 作为选择的大小 （1 个 vCPU，1 GB 内存）。
    1. 对于管理员帐户，选择密码验证并输入惟一的用户名和密码。 对于本教程，您不需要登录到 VM，除非需要进行故障排除。
    
        ![用于创建 VM 的基础知识选项卡](./media/functions-create-vnet/create-vm-1.png)

1. 将移动到**网络**选项卡并输入以下信息：
    1.  创建新的虚拟网络。
    1.  输入的专用地址范围和相应的地址范围中的子网。 子网大小将决定可在应用服务计划中使用的 VM 数量。 如果 IP 寻址和子网不熟悉，还有[介绍了基础知识的文档](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 IP 寻址和子网非常重要在此方案中，因此，我们建议你阅读几篇文章，并观看一些视频联机，直到有意义。 
    
        此示例中，我们要选择 10.10.0.0/16 网络用于 10.10.1.0/24 的子网。 我们正在过度预配和使用 / 16 子网因为很容易计算哪些子网是 10.10.0.0/16 网络中可用。
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. 重新**联网**选项卡上，将公共 IP 设置为**None**。 此步骤中将部署到仅在虚拟网络的 VM 具有访问权限。
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. 创建 VM。 该过程大约需要 5 分钟。
8. 创建 VM 后，请转到其**网络**选项卡并记下为更高版本的专用 IP 地址。 该 VM 不应有公共 IP。

    ![14]

现可在虚拟网络内完全部署的 WordPress 网站。 本站点不能从公共 Internet 访问。

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级版计划中创建函数应用

下一步是在高级版计划中创建函数应用。 高级计划提供的专用应用服务计划的所有优势的无服务器规模。 消耗计划通过创建函数应用不支持虚拟网络集成。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>将函数应用连接到虚拟网络

与托管文件从虚拟网络中的 WordPress 站点，您现在可以连接函数应用到虚拟网络。

1.  在上一步中将函数应用门户中，选择**平台功能**。 然后选择**网络**。

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  选择**单击这里以配置**下**VNet 集成**。

    ![用于配置网络功能状态](./media/functions-create-vnet/Networking-1.png)

1. 在虚拟网络集成页中，选择**添加 VNet （预览版）**。

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  创建函数和应用服务计划以使用新的子网。 请注意，子网大小会限制你可以将其添加到你的应用服务计划的 Vm 总数。 你的虚拟网络将自动路由虚拟网络，因此它并不重要函数已从你的 VM 的不同子网中的子网之间的流量。 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>创建访问你的虚拟网络中的资源的函数

函数应用现在可以访问我们的 WordPress 站点使用的虚拟网络。 因此我们将使用该函数来访问该文件，并为其返回给用户提供服务。 此示例中，我们将使用 WordPress 站点作为 API 和代理为调用函数因为它们是易于设置和可视化。 

可以轻松地使用虚拟网络内部署的任何其他 API。 此外可以使用虚拟网络内部署的 API 的 API 调用的代码使用另一个函数。 虚拟网络内部署的 SQL Server 实例是一个完美示例。

1. 在门户中，打开上一步中的函数应用。
1. 通过选择创建的代理**代理** > **+**。

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. 配置代理名称和路由。 此示例使用"/ 计划"为路由。
1. 填写从之前的 WordPress 站点的 IP，并设置**后端 URL**到 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

现在，如果您试图访问后端 URL 直接通过将其粘贴到新的浏览器选项卡，页将会超时。这是因为您的 WordPress 网站连接到仅在虚拟网络和不是 internet。 如果你的代理 URL 粘贴到浏览器中时，应在虚拟网络内部查看工厂图片 （提取从你的 WordPress 站点）。 

函数应用连接到 internet 和虚拟网络。 代理通过公共 Internet 接收请求，然后作为简单的 HTTP 代理将该请求转发到虚拟网络。 随后，代理通过公共 Internet 将响应转回给你。 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>后续步骤

在高级版计划中运行的函数共享相同的基础应用服务基础结构即高级 V2 计划上的 web 应用。 Web 应用的所有文档都适用于高级计划函数。

* [了解有关在函数中的网络选项的详细信息](./functions-networking-options.md)
* [读取网络常见问题函数](./functions-networking-faq.md)
* [了解有关 Azure 中的虚拟网络的详细信息](../virtual-network/virtual-networks-overview.md)
* [启用网络功能和使用应用服务环境的控制](../app-service/environment/intro.md)
* [使用混合连接连接到独立的本地资源，而无需防火墙更改](../app-service/app-service-hybrid-connections.md)
* [了解有关 Functions 代理的详细信息](./functions-proxies.md)

<!--Image references -->
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
