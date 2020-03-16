---
title: 对 Azure Functions 启用专用站点访问
description: 了解如何为 Azure Functions 设置 Azure 虚拟网络专用站点访问。
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78852012"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>教程：建立 Azure Functions 专用站点访问

本教程介绍如何对 Azure Functions 启用[专用站点访问](./functions-networking-options.md#private-site-access)。 使用专用站点访问可以要求仅从特定的虚拟网络触发函数代码。

需要将函数应用访问限制到特定的虚拟网络时，专用站点访问很有用。 例如，该函数应用可能仅适用于特定组织的员工，或者仅适用于指定虚拟网络中的服务（如另一 Azure 函数、Azure 虚拟机或 AKS 群集）。

如果函数应用需要访问虚拟网络中的 Azure 资源，或者需要通过[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)进行连接，则需要使用[虚拟网络集成](./functions-create-vnet.md)。

本教程将介绍如何为函数应用配置专用站点访问：

> [!div class="checklist"]
> * 创建虚拟机
> * 创建 Azure Bastion 服务
> * 创建 Azure Functions 应用
> * 配置虚拟网络服务终结点
> * 创建并部署 Azure 函数
> * 从虚拟网络外部和内部调用函数

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="topology"></a>拓扑

下图显示了要创建的解决方案的体系结构：

![专用站点访问解决方案的概要体系结构示意图](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>先决条件

若要学习本教程，必须了解 IP 寻址和子网划分。 可以从[这篇介绍了寻址和子网划分基础知识的文章](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)入手。 网上还有其他许多相关文章和视频。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

本教程的第一步是在虚拟网络中创建新的虚拟机。  将函数限制为仅供从虚拟网络内部访问后，该虚拟机将用于访问此函数。

1. 选择“创建资源”按钮。 

2. 在搜索字段中键入 `Windows Server`，在搜索结果中选择“Windows Server”。 

3. 从 Windows Server 选项列表中选择“Windows Server 2019 Datacenter”，然后按“创建”按钮。  

4. 在“基本信息”选项卡中，根据插图下面的表格中的明确说明来使用 VM 设置： 

    >[!div class="mx-imgBorder"]
    >![新 Windows VM 的“基本信息”选项卡](./media/functions-create-private-site-access/create-vm-3.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **订阅** | 订阅 | 要在其下创建资源的订阅。 |
    | [**资源组**](../azure-resource-manager/management/overview.md) | myResourceGroup | 选择用于包含本教程所用资源的资源组。  使用同一个资源组可以在完成本教程后更容易地清理资源。 |
    | **虚拟机名称** | myVM | VM 名称在资源组中需保持唯一 |
    | [**区域**](https://azure.microsoft.com/regions/) | (US) 美国中北部 | 选择与你靠近或者与要访问的函数靠近的区域。 |
    | **公共入站端口** | 无 | 选择“无”以确保没有从 Internet 到 VM 的入站连接。  对 VM 的远程访问将通过 Azure Bastion 服务进行配置。 |

5. 选择“网络”选项卡，然后选择“新建”配置新的虚拟网络。  

    >[!div class="mx-imgBorder"]
    >![为新 VM 创建新的虚拟网络](./media/functions-create-private-site-access/create-vm-networking.png)

6. 在“创建虚拟网络”中，使用插图下面的表格中的设置： 

    >[!div class="mx-imgBorder"]
    >![为新 VM 创建新的虚拟网络](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **名称** | myResourceGroup-vnet | 可以使用为虚拟网络生成的默认名称。 |
    | **地址范围** | 10.10.0.0/16 | 为虚拟网络使用单个地址范围。 |
    | **子网名称** | 教程 | 子网的名称。 |
    | **地址范围**（子网） | 10.10.1.0/24 | 子网大小定义了可将多少个接口添加到子网。 VM 将使用此子网。 一个 `/24` 子网提供 254 个主机地址。 |

7. 选择“确定”以创建虚拟网络。 
8. 返回到“网络”选项卡，确保为“公共 IP”选择“无”。   
9. 选择“管理”选项卡，然后在“诊断存储帐户”中，选择“新建”以创建新的存储帐户。   
10. 在“标识”、“自动关闭”和“备份”部分保留默认值。   
11. 选择“查看 + 创建”  。 验证完成后，选择“创建”。  VM 创建过程需要花费几分钟时间。

## <a name="configure-azure-bastion"></a>配置 Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) 是一个完全托管的 Azure 服务，使用它可以直接从 Azure 门户对虚拟机进行安全的 RDP 和 SSH 访问。 使用 Azure Bastion 服务就无需配置与 RDP 访问相关的网络设置了。

1. 在门户中，选择资源组视图顶部的“添加”。 
2. 在搜索字段中键入“Bastion”。  选择“Bastion”。
3. 选择“创建”以开始执行创建新 Azure Bastion 资源的过程。  “虚拟网络”部分会显示一条错误消息，因为此时还没有 `AzureBastionSubnet` 子网。  该子网将在后续步骤中创建。 使用插图下面的表格中的设置：

    >[!div class="mx-imgBorder"]
    >![开始创建 Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **名称** | myBastion | 新 Bastion 资源的名称 |
    | **区域** | 美国中北部 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |
    | **虚拟网络** | myResourceGroup-vnet | 将在其中创建 Bastion 资源的虚拟网络 |
    | **子网** | AzureBastionSubnet | 虚拟网络中的子网，Bastion 主机资源将部署到该子网。 必须使用名称值 `AzureBastionSubnet` 创建子网。 此值告知 Azure 要将 Bastion 资源部署到哪个子网。 必须使用至少为 `/27` 或更大（`/27`、`/26` 等）的子网。 |

    > [!NOTE]
    > 有关创建 Azure Bastion 资源的详细分步指导，请参阅[创建 Azure Bastion 主机](../bastion/bastion-create-host-portal.md)教程。

4. 创建一个可供 Azure 在其中预配 Azure Bastion 主机的子网。 选择“管理子网配置”会打开一个新窗格，在其中可以定义新的子网。   选择“+ 子网”创建新子网。 
5. 该子网的名称必须是 `AzureBastionSubnet`，子网前缀必须至少为 `/27`。  选择“确定”以创建子网。 

    >[!div class="mx-imgBorder"]
    >![为 Azure Bastion 主机创建子网](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. 在“创建 Bastion”页上，从可用子网列表中选择新建的 `AzureBastionSubnet`。 

    >[!div class="mx-imgBorder"]
    >![创建具有特定子网的 Azure Bastion 主机](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. 选择“查看 + 创建”。  验证完成后，选择“创建”。  创建 Azure Bastion 资源需要花费几分钟时间。

## <a name="create-an-azure-functions-app"></a>创建 Azure Functions 应用

下一步是使用[消耗计划](functions-scale.md#consumption-plan)在 Azure 中创建函数应用。 稍后在本教程中，会将函数代码部署到此资源。

1. 在门户中，选择资源组视图顶部的“添加”。 
2. 选择“计算”>“函数应用” 
3. 在“基本信息”部分，使用下表中指定的函数应用设置。 

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **资源组** | myResourceGroup | 选择用于包含本教程所用资源的资源组。  对函数应用和 VM 使用同一个资源组可以在完成本教程后更容易地清理资源。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 a-z（不区分大小写）、0-9 和 -。 |
    | **发布** | 代码 | 用于发布代码文件或 Docker 容器的选项。 |
    | **运行时堆栈** | 首选语言 | 选择支持你喜欢的函数编程语言的运行时。 |
    | **区域** | 美国中北部 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

    选择页面底部的“下一步:  托管 >”按钮。
4. 在“托管”部分，根据下表中所述选择适当的“存储帐户”、“操作系统”和“计划”。    

    | 设置      | 建议的值  | 说明      |
    | ------------ | ---------------- | ---------------- |
    | **存储帐户** | 全局唯一名称 | 创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](./functions-scale.md#storage-account-requirements)。 |
    | **操作系统** | 首选操作系统 | 系统会根据你的运行时堆栈选择为你预先选择一个操作系统，但你可以根据需要更改该设置。 |
    | **规划** | 消耗 | [托管计划](./functions-scale.md)指示如何缩放函数应用，以及可供每个实例使用的资源。 |
5. 选择“查看 + 创建”  ，以便查看应用配置选择。
6. 选择“创建”  以预配和部署函数应用。

## <a name="configure-access-restrictions"></a>配置访问限制

下一步是配置[访问限制](../app-service/app-service-ip-restrictions.md)，以确保只有虚拟网络中的资源能够调用该函数。

通过在函数应用与指定的虚拟网络之间创建 Azure 虚拟网络[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)，来启用[专用站点](functions-networking-options.md#private-site-access)访问。 访问限制是通过服务终结点实现的。 服务终结点确保只有源自指定虚拟网络内部的流量可以访问指定的资源。 在本例中，指定的资源是 Azure 函数。

1. 在函数应用中，转到“平台功能”选项卡。  选择“网络”部分标题下面的“网络”链接，打开“网络功能状态”部分。  
2. “网络功能状态”页是配置“Azure Front Door”、“Azure CDN”和“访问限制”的起点。  选择“配置访问限制”以配置专用站点访问。 
3. 在“访问限制”页上，你会看到只是实施了默认限制。  默认限制不会对访问函数应用施加任何限制。  选择“添加规则”以创建专用站点访问限制配置。 
4. 在“添加访问限制”窗格中，从“类型”下拉框中选择“虚拟网络”，然后选择前面创建的虚拟网络和子网。   
5. “访问限制”页现在会显示有新的限制。  “终结点状态”可能需要在几秒钟后才会从 `Disabled` 更改为 `Provisioning`，再更改为 `Enabled`。 

    >[!IMPORTANT]
    > 每个函数应用都有一个用于管理函数应用部署的[高级工具 (Kudu) 站点](../app-service/app-service-ip-restrictions.md#scm-site)。 可从如下所示的 URL 访问此站点：`<FUNCTION_APP_NAME>.scm.azurewebsites.net`。 由于尚未在此部署站点上启用访问限制，因此仍可以从本地开发人员工作站或生成服务部署项目代码，而无需在虚拟网络中预配代理。

## <a name="access-the-functions-app"></a>访问函数应用

1. 返回到前面创建的函数应用。  在“概述”部分复制 URL。 

    >[!div class="mx-imgBorder"]
    >![获取函数应用 URL](./media/functions-create-private-site-access/access-function-overview.png)

2. 如果现在尝试从虚拟网络外部的计算机访问该函数应用，将会显示“HTTP 403”页，指出该应用已停止。  该应用其实并未停止。 响应实际上是“HTTP 403 IP 已禁止”状态。
3. 现在，从前面创建的虚拟机（已连接到虚拟网络）访问你的函数。 若要从 VM 访问该站点，需要通过 Azure Bastion 服务连接到该 VM。  先选择“连接”，再选择“Bastion”。  
4. 提供登录到虚拟机所需的用户名和密码。  选择“连接”  。 此时会弹出一个新的浏览器窗口，使你可以与虚拟机交互。
5. 由于此 VM 正在通过虚拟网络访问函数，因此可以从 VM 上的 Web 浏览器访问该站点。  必须注意的是，尽管只能从指定的虚拟网络内访问该函数应用，但仍保留了一个公共 DNS 条目。 如上所示，尝试访问站点会导致出现一个 HTTP 403 响应。

## <a name="create-a-function"></a>创建函数

本教程的下一步是创建 HTTP 触发的 Azure 函数。 通过 HTTP GET 或 POST 调用函数会导致出现“Hello, {name}”响应。  

1. 遵循以下快速入门之一，以便创建和部署 Azure Functions 应用。

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [命令行](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. 发布 Azure Functions 项目时，请选择前面在本教程中创建的函数应用资源。
3. 验证是否已部署该函数。

    >[!div class="mx-imgBorder"]
    >![函数列表中已部署的函数](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>直接调用函数

1. 若要测试对函数的访问，需要复制函数 URL。 选择已部署的函数，然后选择“</> 获取函数 URL”。  然后单击“复制”按钮将 URL 复制到剪贴板。 

    >[!div class="mx-imgBorder"]
    >![复制函数 URL](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > 运行函数时，门户中会显示一条运行时错误，指出函数运行时无法启动。 尽管出现了此消息，但函数应用实际上正在运行。 出现此错误的原因是实施了新的访问限制，这些限制会阻止门户通过查询对运行时进行检查。

2. 将 URL 粘贴到 Web 浏览器中。 如果现在尝试从虚拟网络外部的计算机访问该函数应用，将会收到 HTTP 403 响应，指出该应用已停止。

## <a name="invoke-the-function-from-the-virtual-network"></a>从虚拟网络调用函数

在虚拟网络中配置的 VM 上通过 Web 浏览器（使用 Azure Bastion 服务）访问该函数会返回成功响应！

>[!div class="mx-imgBorder"]
>![通过 Azure Bastion 访问 Azure 函数](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤


> [!div class="nextstepaction"]
> [详细了解 Functions 中的网络选项](./functions-networking-options.md)
