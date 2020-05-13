---
title: 将 Azure Functions 与 Azure 虚拟网络集成
description: 演示如何将函数连接到 Azure 虚拟网络的分步教程
author: alexkarcher-msft
ms.topic: article
ms.date: 4/23/2020
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: e1babfa188a29e79cb52cd14af19d552123345f1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122620"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>教程：将 Functions 与 Azure 虚拟网络集成

本教程介绍如何使用 Azure Functions 连接到 Azure 虚拟网络中的资源。 你将创建一个函数，该函数可访问 internet 和在虚拟网络中运行 WordPress 的 VM。

> [!div class="checklist"]
> * 在高级计划中创建函数应用
> * 将 WordPress 站点部署到虚拟网络中的 VM
> * 将函数应用连接到虚拟网络
> * 创建函数代理以访问 WordPress 资源
> * 从虚拟网络内部请求 WordPress 文件

## <a name="topology"></a>拓扑

下图显示了您创建的解决方案的体系结构：

 ![虚拟网络集成的 UI](./media/functions-create-vnet/topology.png)

高级计划中运行的函数与 Azure App Service 中的 web 应用具有相同的承载功能，其中包括 VNet 集成功能。 若要了解有关 VNet 集成的详细信息，包括疑难解答和高级配置，请参阅将[应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>必备条件

对于本教程，请务必了解 IP 寻址和子网。 可以从本文开始[，其中介绍了寻址和子网的基本知识](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 其他更多的文章和视频可在线获得。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级计划中创建 function app

首先，在[高级计划]中创建一个函数应用。 此计划提供无服务器规模，同时支持虚拟网络集成。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

您可以通过选择右上角的图钉图标将函数应用固定到仪表板。 在创建 VM 后，可以更轻松地返回到此函数应用。

## <a name="create-a-vm-inside-a-virtual-network"></a>在虚拟网络中创建 VM

接下来，创建一个在虚拟网络中运行 WordPress 的预配置 VM （[WORDPRESS LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware）。 使用 WordPress VM 的原因是成本较低且方便。 此同一方案适用于虚拟网络中的任何资源，例如 REST Api、应用服务环境和其他 Azure 服务。 

1. 在门户中，在左侧导航窗格中选择 " **+ 创建资源**"，在搜索字段类型中选择， `WordPress LEMP7 Max Performance` 然后按 enter。

1. 在搜索结果中选择 " **WORDPRESS LEMP Max 性能**"。 选择**WORDPRESS LEMP Max Performance For CentOS**的**软件计划，并选择 "** **创建**"。

1. 在 "**基本**信息" 选项卡中，使用映像下下表中指定的 VM 设置：

    ![用于创建 VM 的 "基本信息" 选项卡](./media/functions-create-vnet/create-vm-1.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 你的订阅 | 用于创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择 `myResourceGroup` 或你用 function app 创建的资源组。 对于 function app、WordPress VM 和托管计划使用同一资源组，可以在完成本教程后更轻松地清理资源。 |
    | **虚拟机名称** | VNET-Wordpress | VM 名称在资源组中必须是唯一的 |
    | **[区域](https://azure.microsoft.com/regions/)** | 东欧西欧 | 在访问 VM 的函数附近或附近选择一个区域。 |
    | **大小** | B1s | 选择 "**更改大小**"，然后选择 B1s 标准映像，其中包含1个 vCPU 和 1 GB 的内存。 |
    | **身份验证类型** | 密码 | 若要使用密码身份验证，还必须指定**用户名**、安全**密码**，然后**确认密码**。 对于本教程，无需登录到 VM，除非需要进行故障排除。 |

1. 选择 "**网络**" 选项卡，然后在 "配置虚拟网络" 下选择 "**新建**"。

1. 在 "**创建虚拟网络**" 中，使用映像下表中的设置：

    ![创建 VM 的 "网络" 选项卡](./media/functions-create-vnet/create-vm-2.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **名称** | myResourceGroup-vnet | 你可以使用为虚拟网络生成的默认名称。 |
    | **地址范围** | 10.10.0.0/16 | 为虚拟网络使用单个地址范围。 |
    | **子网名称** | 教程-网络 | 子网的名称。 |
    | **地址范围**（子网） | 10.10.1.0/24   | 子网大小定义可向子网添加多少个接口。 此子网由 WordPress 站点使用。  `/24`子网提供254主机地址。 |

1. 选择 **"确定"** 以创建虚拟网络。

1. 返回 "**网络**" 选项卡，选择 "**无**" 作为**公共 IP**。

1. 选择 "**管理**" 选项卡，然后在 "**诊断存储帐户**" 中，选择你用 Function app 创建的存储帐户。

1. 选择“查看 + 创建”  。 验证完成后，选择 "**创建**"。 VM 创建过程需花费几分钟时间。 创建的 VM 只能访问虚拟网络。

1. 创建 VM 后，选择 "**前往资源**" 查看新 vm 的页面，然后在 "**设置**" 下选择 "**网络**"。

1. 验证没有**公共 IP**。 记下**专用 IP**，它用于从函数应用连接到 VM。

    ![VM 中的网络设置](./media/functions-create-vnet/vm-networking.png)

你现在已完全在虚拟网络中部署了 WordPress 站点。 无法从公共 internet 访问此站点。

## <a name="connect-your-function-app-to-the-virtual-network"></a>将函数应用连接到虚拟网络

通过在虚拟网络中的 VM 上运行 WordPress 站点，你现在可以将函数应用连接到该虚拟网络。

1. 在新的函数应用中，选择左侧菜单中的 "**网络**"。

1. 在 " **VNet 集成**" 下，选择 **"单击此处进行配置**"。

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="在 function app 中选择网络":::

1. 在 " **VNET 集成**" 页上，选择 "**添加 VNET**"。

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="添加 VNet 集成预览":::

1. 在 "**网络功能状态**" 中，使用映像下表中的设置：

    ![定义函数应用虚拟网络](./media/functions-create-vnet/networking-3.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **虚拟网络** | MyResourceGroup-vnet | 此虚拟网络是你之前创建的网络。 |
    | **子网** | 创建新子网 | 在虚拟网络中创建一个子网，以便函数应用使用。 必须将 VNet 集成配置为使用空子网。 函数使用不同于 VM 的子网并不重要。 虚拟网络自动在两个子网之间路由流量。 |
    | **子网名称** | 函数-Net | 新子网的名称。 |
    | **虚拟网络地址块** | 10.10.0.0/16 | 选择 WordPress 站点使用的同一个地址块。 只应定义一个地址块。 |
    | **地址范围** | 10.10.2.0/24   | 子网大小限制高级计划函数应用可以向外扩展到的实例总数。 此示例使用 `/24` 具有254个可用主机地址的子网。 此子网过度预配，但易于计算。 |

1. 选择 **"确定"** 以添加子网。 关闭 " **VNet 集成**" 和 "**网络功能状态**" 页，返回到 "function app" 页。

函数应用现在可以访问运行 WordPress 站点的虚拟网络。 接下来，使用[Azure Functions 代理](functions-proxies.md)从 WordPress 站点返回文件。

## <a name="create-a-proxy-to-access-vm-resources"></a>创建用于访问 VM 资源的代理

启用 VNet 集成后，可以在 function app 中创建一个代理，以将请求转发到虚拟网络中运行的 VM。

1. 在 function app 中，从左侧菜单中选择 "**代理**"，然后选择 "**添加**"。 使用映像下表中的代理设置：

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="定义代理设置":::

    | 设置  | 建议的值  | 说明      |
    | -------- | ---------------- | ---------------- |
    | **名称** | PlAnT | 该名称可以是任何值。 它用于标识代理。 |
    | **路由模板** | /plant | 映射到 VM 资源的路由。 |
    | **后端 URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | 将替换 `<YOUR_VM_IP>` 为前面创建的 WORDPRESS VM 的 IP 地址。 此映射返回站点中的单个文件。 |

1. 选择 "**创建**"，将代理添加到 function app。

## <a name="try-it-out"></a>试试看

1. 在浏览器中，尝试访问用作**后端 url**的 URL。 如预期那样，请求会超时。发生超时的原因是你的 WordPress 站点仅连接到你的虚拟网络而不是 internet。

1. 复制新代理中的 "**代理 URL** " 值，并将其粘贴到浏览器的地址栏中。 返回的映像来自虚拟网络内运行的 WordPress 站点。

    ![从 WordPress 站点返回的植物映像文件](./media/functions-create-vnet/plant.png)

函数应用已连接到 internet 和虚拟网络。 代理正在通过公共 internet 接收请求，然后充当简单的 HTTP 代理，以便将该请求转发到连接的虚拟网络。 然后，代理通过 internet 将响应中继回给你。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，WordPress 站点充当 API，该 API 是通过在 function app 中使用代理来调用的。 此方案可以很好地实现教程，因为它易于设置和可视化。 你可以使用虚拟网络中部署的任何其他 API。 你还可以创建一个函数，该函数包含调用虚拟网络内部署的 Api 的代码。 一个更现实的方案是使用数据客户端 Api 调用部署在虚拟网络中的 SQL Server 实例的函数。

高级计划中运行的函数在 PremiumV2 计划上共享与 web 应用相同的基础应用服务基础结构。 [Azure App Service 中的 web 应用](../app-service/overview.md)的所有文档都适用于高级计划功能。

> [!div class="nextstepaction"]
> [了解有关函数中的网络选项的详细信息](./functions-networking-options.md)

[高级计划]: functions-scale.md#premium-plan
