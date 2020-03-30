---
title: 将 Azure 功能与 Azure 虚拟网络集成
description: 分步教程，演示如何将函数连接到 Azure 虚拟网络
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433212"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>教程：将 Functions 与 Azure 虚拟网络集成

本教程演示如何使用 Azure 函数连接到 Azure 虚拟网络中的资源。 您将创建一个函数，该函数可以访问 Internet 和在虚拟网络中运行 WordPress 的 VM。

> [!div class="checklist"]
> * 在高级计划中创建函数应用
> * 将 WordPress 站点部署到虚拟网络中的 VM
> * 将功能应用连接到虚拟网络
> * 创建函数代理以访问 WordPress 资源
> * 从虚拟网络内部请求 WordPress 文件

## <a name="topology"></a>拓扑

下图显示了您创建的解决方案的体系结构：

 ![用于虚拟网络集成的 UI](./media/functions-create-vnet/topology.png)

在高级计划中运行的功能具有与 Azure 应用服务中的 Web 应用相同的托管功能，其中包括 VNet 集成功能。 要了解有关 VNet 集成（包括故障排除和高级配置）的更多信息，请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>先决条件

在本教程中，了解 IP 寻址和子网非常重要。 你可以从本文开始[，介绍寻址和子网的基础知识](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 更多文章和视频可在线获取。

如果没有 Azure 订阅，请先创建[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级计划中创建函数应用

首先，在[高级计划中]创建函数应用。 此计划提供无服务器扩展，同时支持虚拟网络集成。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

您可以通过选择右上角的固定图标将功能应用固定到仪表板。 固定使创建 VM 后更容易返回到此函数应用。

## <a name="create-a-vm-inside-a-virtual-network"></a>在虚拟网络内创建 VM

接下来，创建一个预配置的 VM，在虚拟网络内运行 WordPress（通过 Jetware 运行[WordPress LEMP7 最大性能](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)）。 WordPress VM 的使用是因为它成本低且方便。 此相同的方案适用于虚拟网络中的任何资源，如 REST API、应用服务环境和其他 Azure 服务。 

1. 在门户中，选择 " 在左侧导航窗格中，在搜索字段类型`WordPress LEMP7 Max Performance`中**创建资源**"，然后按 Enter。

1. 在搜索结果中选择 **"字压 LEMP 最大性能**"。 选择**Wordpress LEMP CentOS 的最大性能**软件计划作为**软件计划**，然后选择"**创建**"。

1. 在 **"基础知识"** 选项卡中，使用图像下表中指定的 VM 设置：

    ![用于创建 VM 的基础知识选项卡](./media/functions-create-vnet/create-vm-1.png)

    | 设置      | 建议的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 创建资源的订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 选择`myResourceGroup`，或者选择使用函数应用创建的资源组。 对函数应用 WordPress VM 和托管计划使用相同的资源组，在完成本教程后，可以更轻松地清理资源。 |
    | **虚拟机名称** | VNET-文字印刷机 | VM 名称在资源组中必须是唯一的 |
    | **[地区](https://azure.microsoft.com/regions/)** | （欧洲）西欧 | 选择您附近的区域或访问 VM 的功能附近的区域。 |
    | **大小** | B1s | 选择 **"更改大小**"，然后选择具有 1 个 vCPU 和 1 GB 内存的 B1 标准映像。 |
    | **身份验证类型** | 密码 | 要使用密码身份验证，还必须指定**用户名**、安全**密码**，然后**确认密码**。 在本教程中，除非需要排除故障，否则无需登录到 VM。 |

1. 选择"**网络**"选项卡，在"配置虚拟网络"下选择 **"创建新**"。

1. 在 **"创建虚拟网络"** 中，使用图像下方表中的设置：

    ![创建 VM 的网络选项卡](./media/functions-create-vnet/create-vm-2.png)

    | 设置      | 建议的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **名称** | 我的资源组-vnet | 您可以使用为虚拟网络生成的默认名称。 |
    | **地址范围** | 10.10.0.0/16 | 对虚拟网络使用单个地址范围。 |
    | **子网名称** | 教程网 | 子网的名称。 |
    | **地址范围**（子网） | 10.10.1.0/24   | 子网大小定义可以添加到子网的接口数。 WordPress 网站使用此子网。  `/24`子网提供 254 个主机地址。 |

1. 选择 **"确定"** 以创建虚拟网络。

1. 返回 **"网络"** 选项卡，为公共**IP**选择 **"无**"。

1. 选择 **"管理**"选项卡，然后在**诊断存储帐户**中，选择使用函数应用创建的存储帐户。

1. 选择“查看 + 创建”****。 验证完成后，选择 **"创建**"。 VM 创建过程需要几分钟时间。 创建的 VM 只能访问虚拟网络。

1. 创建 VM 后，选择 **"转到资源"** 以查看新 VM 的页面，然后在 **"设置"** 下选择 **"网络**"。

1. 验证没有**公共 IP。** 记下**专用 IP**，用于从函数应用连接到 VM。

    ![VM 中的网络设置](./media/functions-create-vnet/vm-networking.png)

现在，WordPress 站点完全部署在虚拟网络中。 本网站无法从公共互联网上访问。

## <a name="connect-your-function-app-to-the-virtual-network"></a>将功能应用连接到虚拟网络

在虚拟网络中的 VM 中运行 WordPress 站点后，您现在可以将函数应用连接到该虚拟网络。

1. 在新功能应用中，选择**平台功能** > **"网络**"。

    ![在功能应用中选择网络](./media/functions-create-vnet/networking-0.png)

1. 在**VNet 集成**下，选择 **"单击此处配置**"。

    ![配置网络功能的状态](./media/functions-create-vnet/Networking-1.png)

1. 在虚拟网络集成页上，选择 **"添加 VNet（预览）"。**

    ![添加 VNet 集成预览](./media/functions-create-vnet/networking-2.png)

1. 在 **"网络功能状态**"中，使用图像下方表中的设置：

    ![定义功能应用虚拟网络](./media/functions-create-vnet/networking-3.png)

    | 设置      | 建议的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **虚拟网络** | MyResourceGroup-vnet | 此虚拟网络是您之前创建的虚拟网络。 |
    | **子** | 创建新子网 | 在虚拟网络中创建一个子网，供函数应用使用。 必须将 VNet 集成配置为使用空子网。 函数使用与 VM 不同的子网并不重要。 虚拟网络自动路由两个子网之间的流量。 |
    | **子网名称** | 功能网 | 新子网的名称。 |
    | **虚拟网络地址块** | 10.10.0.0/16 | 选择 WordPress 网站使用的相同地址块。 应只定义一个地址块。 |
    | **地址范围** | 10.10.2.0/24   | 子网大小限制高级计划功能应用可以扩展到的实例总数。 此示例使用具有`/24`254 个可用主机地址的子网。 此子网预配过高，但易于计算。 |

1. 选择 **"确定"** 以添加子网。 关闭 VNet 集成和网络功能状态页面以返回到功能应用页面。

功能应用现在可以访问运行 WordPress 站点的虚拟网络。 接下来，使用[Azure 函数代理](functions-proxies.md)从 WordPress 站点返回文件。

## <a name="create-a-proxy-to-access-vm-resources"></a>创建代理以访问 VM 资源

启用 VNet 集成后，您可以在函数应用中创建代理，将请求转发到虚拟网络中运行的 VM。

1. 在函数应用中，选择 **"代理** > **+**"，然后使用图像下方表中的代理设置：

    ![定义代理设置](./media/functions-create-vnet/create-proxy.png)

    | 设置  | 建议的值  | 描述      |
    | -------- | ---------------- | ---------------- |
    | **名称** | PlAnT | 名称可以是任何值。 它用于标识代理。 |
    | **路由模板** | /工厂 | 映射到 VM 资源的路由。 |
    | **后端 URL** | http：//<YOUR_VM_IP>/wp-内容/主题/21/资产/图像/标题.jpg | 替换为`<YOUR_VM_IP>`之前创建的 WordPress VM 的 IP 地址。 此映射从站点返回单个文件。 |

1. 选择 **"创建**"以将代理添加到函数应用。

## <a name="try-it-out"></a>试用

1. 在浏览器中，尝试访问用作后端 URL 的**URL。** 如预期的那样，请求超时。超时的原因是您的 WordPress 站点仅连接到您的虚拟网络，而不是互联网连接。

1. 从新代理复制**代理 URL**值并将其粘贴到浏览器的地址栏中。 返回的图像来自虚拟网络中运行的 WordPress 站点。

    ![从 WordPress 网站返回的工厂图像文件](./media/functions-create-vnet/plant.png)

您的功能应用已连接到互联网和虚拟网络。 代理通过公共 Internet 接收请求，然后充当简单的 HTTP 代理，将该请求转发到连接的虚拟网络。 然后，代理通过互联网公开将响应转发给您。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，WordPress 站点用作在函数应用中使用代理调用的 API。 此方案是一个很好的教程，因为它易于设置和可视化。 您可以使用部署在虚拟网络中的任何其他 API。 您还可以创建一个函数，其代码调用部署在虚拟网络中的 API。 更现实的情况是，使用数据客户端 API 调用部署在虚拟网络中的 SQL Server 实例的函数。

在高级计划中运行的功能与高级 V2 计划中的 Web 应用共享相同的基础应用服务基础结构。 [Azure 应用服务中 Web 应用](../app-service/overview.md)的所有文档都适用于高级计划功能。

> [!div class="nextstepaction"]
> [了解有关函数中的网络选项的详细信息](./functions-networking-options.md)

[保费计划]: functions-scale.md#premium-plan
