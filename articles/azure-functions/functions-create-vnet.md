---
title: 将 Azure Functions 与 Azure 虚拟网络集成
description: 分步教程演示如何将函数连接到 Azure 虚拟网络
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612891"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>教程： 将与 Azure 虚拟网络集成函数

本教程演示如何使用 Azure Functions 连接到 Azure 虚拟网络中的资源。 你将创建具有对这两个 internet 和虚拟网络中运行 WordPress 的 VM 访问的函数。

> [!div class="checklist"]
> * 在高级版计划中创建函数应用
> * 在虚拟网络中将 WordPress 站点部署到 VM
> * 函数应用连接到虚拟网络
> * 创建函数代理以访问 WordPress 资源
> * 请求从虚拟网络中的 WordPress 文件

> [!NOTE]  
> 本教程中的高级计划创建 function app。 此托管计划目前处于预览状态。 有关详细信息，请参阅[高级版计划]。

## <a name="topology"></a>拓扑

下图显示了你创建的解决方案体系结构：

 ![虚拟网络集成的用户界面](./media/functions-create-vnet/topology.png)

在高级版计划中运行的函数具有与 web 应用相同的托管功能在 Azure 应用服务中，其中包括 VNet 集成功能。 若要详细了解 VNet 集成，包括故障排除和高级配置，请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="prerequisites"></a>先决条件

对于本教程中，它是必须了解 IP 寻址和子网。 可以使用启动[本文介绍了寻址和子网的基础知识](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)。 很多更多文章和视频，请联机参阅。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-function-app-in-a-premium-plan"></a>在高级版计划中创建函数应用

首先，创建函数应用中的[高级版计划]。 此计划在支持虚拟网络集成的同时提供无服务器的规模。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

可以通过选择右上角的图钉图标固定到仪表板的函数应用。 固定可以更轻松地在创建 VM 后，返回到此函数应用。

## <a name="create-a-vm-inside-a-virtual-network"></a>创建虚拟网络中的 VM

接下来，创建虚拟网络中运行 WordPress 的预配置的 VM ([WordPress LEMP7 最大性能](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)jetware 的适用情况)。 由于其低成本和方便使用 WordPress VM。 这同一情况下适用于虚拟网络，如 REST Api、 应用服务环境和其他 Azure 服务中的任何资源。 

1. 在门户中，选择 **+ 创建资源**在左侧的导航窗格中，在搜索字段中键入`WordPress LEMP7 Max Performance`，然后按 Enter。

1. 选择**Wordpress LEMP Max Performance**在搜索结果中。 选择的软件计划**针对 CentOS 的 Wordpress LEMP Max Performance**作为**软件计划**，然后选择**创建**。

1. 在中**基础知识**选项卡上，使用图像下的表中指定的 VM 设置：

    ![用于创建 VM 的基础知识选项卡](./media/functions-create-vnet/create-vm-1.png)

    | 设置      | 建议的值  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 你的资源在其下创建的订阅。 | 
    | **[资源组](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | 选择`myResourceGroup`，或使用 function app 创建的资源组。 为函数应用，WordPress VM 使用相同的资源组和托管计划轻松完成本教程时清理资源。 |
    | **虚拟机名称** | VNET-Wordpress | VM 名称必须是唯一的资源组中 |
    | **[区域](https://azure.microsoft.com/regions/)** | （欧洲）西欧 | 选择你附近的区域或附近函数的访问 VM。 |
    | **大小** | B1s | 选择**更改大小**，然后选择 B1s 标准映像，具有 1 个 vCPU 和 1 GB 的内存。 |
    | **身份验证类型** | 密码 | 若要使用密码身份验证，还必须指定**用户名**，一种安全**密码**，然后**确认密码**。 对于本教程，您不需要登录到 VM，除非需要进行故障排除。 |

1. 选择**联网**选项卡，然后在配置虚拟网络下选择**新建**。

1. 在中**创建虚拟网络**，使用图像下的表中的设置：

    ![网络选项卡创建 VM](./media/functions-create-vnet/create-vm-2.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **名称** | myResourceGroup-vnet | 可以使用生成的虚拟网络的默认名称。 |
    | **地址范围** | 10.10.0.0/16 | 为虚拟网络中使用单个地址范围。 |
    | **子网名称** | 教程-Net | 子网的名称。 |
    | **地址范围**（子网） | 10.10.1.0/24   | 子网大小定义多少个接口可以添加到子网。 WordPress 站点使用此子网。  一个`/24`子网提供 254 个主机地址。 |

1. 选择**确定**创建虚拟网络。

1. 回到**联网**选项卡上，选择**None**有关**公共 IP**。

1. 选择**管理**选项卡上，然后在**诊断存储帐户**，选择与你的函数应用创建的存储帐户。

1. 选择“查看 + 创建”  。 验证完成后，选择**创建**。 VM 创建过程需要几分钟时间。 将创建的 VM 只能访问虚拟网络。

1. 创建 VM 后，请选择**转到资源**若要查看的页面，为新的 VM，然后选择**网络**下**设置**。

1. 验证是否有任何**公共 IP**。 记下**专用 IP**，可用于从函数应用连接到 VM。

    ![在 VM 中的网络设置](./media/functions-create-vnet/vm-networking.png)

现可在虚拟网络内完全部署的 WordPress 网站。 此站点不是可从公共 internet 访问。

## <a name="connect-your-function-app-to-the-virtual-network"></a>将函数应用连接到虚拟网络

与在虚拟网络中的 VM 中运行的 WordPress 站点，现在可以为该虚拟网络连接 function app。

1. 在新 function app 中，选择**平台功能** > **网络**。

    ![在 function app 中选择网络](./media/functions-create-vnet/networking-0.png)

1. 下**VNet 集成**，选择**单击这里以配置**。

    ![用于配置网络功能状态](./media/functions-create-vnet/Networking-1.png)

1. 在虚拟网络集成页中，选择**添加 VNet （预览版）** 。

    ![添加 VNet 集成预览版](./media/functions-create-vnet/networking-2.png)

1. 在中**网络功能状态**，使用图像下的表中的设置：

    ![将函数应用虚拟网络定义](./media/functions-create-vnet/networking-3.png)

    | 设置      | 建议的值  | 描述      |
    | ------------ | ---------------- | ---------------- |
    | **虚拟网络** | MyResourceGroup-vnet | 此虚拟网络是前面创建的一个。 |
    | **子网** | 创建新的子网 | 在函数应用使用的虚拟网络中创建子网。 VNet 集成必须配置为使用空的子网。 它并不重要函数使用比 VM 的不同子网。 虚拟网络会自动将路由两个子网之间的流量。 |
    | **子网名称** | Function-Net | 新子网的名称。 |
    | **虚拟网络地址块** | 10.10.0.0/16 | 选择 WordPress 站点使用的同一个地址块。 你只应具有定义的一个地址块。 |
    | **地址范围** | 10.10.2.0/24   | 子网大小限制高级计划函数应用可以横向扩展到的实例的总数。 此示例使用`/24`254 个可用的主机地址的子网。 此子网是过度预配，但计算更容易。 |

1. 选择**确定**添加子网。 关闭要返回到函数应用页的 VNet 集成和网络功能状态页。

函数应用现在可以访问在其中运行 WordPress 站点的虚拟网络。 接下来，使用[Azure Functions 代理](functions-proxies.md)若要将文件返回从 WordPress 网站。

## <a name="create-a-proxy-to-access-vm-resources"></a>创建访问 VM 资源的代理帐户

启用 VNet 集成，可以在函数应用将请求转发到虚拟网络中运行的 VM 中创建代理。

1. 在 function app 中，选择**代理** >  **+** ，然后在图像下的表中使用的代理设置：

    ![定义代理设置](./media/functions-create-vnet/create-proxy.png)

    | 设置  | 建议的值  | 说明      |
    | -------- | ---------------- | ---------------- |
    | **名称** | 工厂 | 名称可以是任何值。 它用于标识代理。 |
    | **路由模板** | /plant | 映射到 VM 资源的路由。 |
    | **后端 URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | 替换为`<YOUR_VM_IP>`WordPress VM 之前创建的 IP 地址。 此映射返回从站点的单个文件。 |

1. 选择**创建**将代理添加到函数应用。

## <a name="try-it-out"></a>试用

1. 在浏览器中，尝试访问作为所用的 URL**后端 URL**。 按预期运行，该请求将会超时。因为你的 WordPress 站点仅连接到虚拟网络并不是 internet 发生超时。

1. 复制**代理 URL**值从新代理，并将其粘贴到你的浏览器的地址栏。 返回的映像是从虚拟网络内部运行的 WordPress 站点。

    ![返回从 WordPress 站点的工厂图像文件](./media/functions-create-vnet/plant.png)

函数应用连接到 internet 和虚拟网络。 代理是通过公共 internet，接收请求，然后充当简单的 HTTP 代理，该请求转发到连接的虚拟网络。 代理然后中继回给你的响应公开通过 internet。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，WordPress 站点用作 API 调用的函数应用中使用代理。 因为这样可以轻松设置和可视化，此方案可以很好的教程。 您可以使用虚拟网络内部署的任何其他 API。 您可能也已创建的函数调用的虚拟网络内部署的 Api 的代码。 更现实的方案是使用数据的客户端 Api 调用在虚拟网络中部署的 SQL Server 实例的函数。

在高级版计划中运行的函数共享相同的基础应用服务基础结构即高级 V2 计划上的 web 应用。 所有的文档[Azure 应用服务中的 web 应用](../app-service/overview.md)适用于高级计划函数。

> [!div class="nextstepaction"]
> [了解有关在函数中的网络选项的详细信息](./functions-networking-options.md)

[高级版计划]: functions-scale.md#premium-plan
