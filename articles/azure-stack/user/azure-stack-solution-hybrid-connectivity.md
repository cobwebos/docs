---
title: 配置与 Azure 和 Azure 堆栈的混合云连接 |Microsoft 文档
description: 了解如何使用 Azure 和 Azure 堆栈配置混合云连接。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8b5f59d3fea402efa50bdafd7fc0439a93051e69
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>教程： 使用 Azure 和 Azure 堆栈配置混合云连接

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在全球 Azure 和 Azure 堆栈使用混合连接模式中与安全性，可以访问资源。 

在本教程中，你将生成到示例环境：

> [!div class="checklist"]
> - 保留在本地的数据的隐私或法规要求，但有权访问全局 Azure 资源。
> - 在全球 Azure 中使用云缩放应用程序部署和资源时维护旧系统。

## <a name="prerequisites"></a>必备组件

几个组件生成混合连接部署所需，可能需要一些时间来准备。 

Azure OEM/硬件合作伙伴可以部署生产 Azure 堆栈和所有用户可能都部署 ASDK。 Azure 堆栈运算符还必须部署 App Service、 创建计划，并提供、 创建租户订阅，并添加 Windows Server 2016 映像。 

如果已准备好其中的某些组件，请确保它们符合要求，然后开始操作。

本主题还假设你对 Azure 和 Azure Stack 有一定的了解。 若要在了解详情之后再继续，请务必从以下主题着手：
 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
 - 创建[Web 应用](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal)在 Azure 中。 记下新 Web 应用的 URL，因为稍后需要用到。

### <a name="azure-stack"></a>Azure Stack
 - 使用你的生产 Azure 堆栈或部署 Azure 堆栈开发工具包按以下链接：
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - 安装过程通常需要几个小时才能完成，因此请相应地做好规划。
 - 将[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服务部署到 Azure Stack。 
 - [创建计划/提供](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)Azure 堆栈环境中。 
 - [创建租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)Azure 堆栈环境中。 


### <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

 - 确认 VPN 设备有一个面向外部的公共 IPv4 地址。 此 IP 地址不得位于 NAT 之后。
 - 确保所有资源都部署在每个位置的区域相同。

#### <a name="example-values"></a>示例值

本文中的示例使用以下值。 这些值可用于创建测试环境或引用它们以更好地了解这篇文章中的示例。 有关通用 VPN 网关设置的详细信息，请参阅[关于 VPN 网关设置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

连接规范：
 - **VPN 类型**：基于路由
 - **连接类型**： 站点到站点 (IPsec)
 - **网关类型**：VPN
 - **Azure 的连接名称**: Azure 的网关-AzureStack-S2SGateway （门户将自动填充此值）
 - **Azure 堆栈连接名称**: AzureStack-网关-Azure-S2SGateway （门户将自动填充此值）
 - **共享的密钥**: VPN 硬件，具有匹配连接两端的值与任何兼容
 - **订阅**： 任何首选订阅
 - **资源组**： 测试基础结构

| Azure/Azure 堆栈连接 | 名称 | 子网 | IP 地址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure 堆栈 vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虚拟网络网关 | Azure 网关 |  |  |
| Azure 堆栈虚拟网络网关 | AzureStack 网关 |  |  |
| 使用 azure 公共 IP | Azure GatewayPublicIP |  | 在创建确定 |
| Azure 堆栈的公共 IP | AzureStack GatewayPublicIP |  | 在创建确定 |
| Azure 的本地网络网关 | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Azure 堆栈的公共 IP 值 |
| Azure 堆栈本地网络网关 | Azure S2SGateway<br>10.100.102.0/23 |  | 使用 azure 公共 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>在全局 Azure 和 Azure 堆栈中创建虚拟网络

> [!note]  
> 你必须确保不存在的 Ip 在 Azure 或 Azure 堆栈 vNet 地址空间重叠。 

若要通过使用 Azure 门户，在资源管理器部署模型创建 vNet。 如果是在教程中使用这些步骤，请使用[示例值](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)。 如果并非在教程中使用这些步骤，请务必将其中的值替换为自己的值。 

1. 从浏览器导航到 [Azure 门户](http://portal.azure.com/)并使用 Azure 帐户登录。
2. 单击“创建资源”。 在**搜索 marketplace**字段中，输入`virtual network`'。 找到**虚拟网络**从返回的列表和打开**虚拟网络**页。
3. 虚拟网络页的底部附近从**选择部署模型**列表中，选择**资源管理器**，然后选择**创建**。 这会打开“创建虚拟网络”页。
4. 在“创建虚拟网络”页上，配置 VNet 设置。 填写字段时，如果在字段中输入的字符有效，红色感叹号标记会变成绿色对钩标记。
5. 从重复这些步骤**租户门户**Azure 堆栈。

## <a name="add-a-gateway-subnet"></a>添加网关子网

将虚拟网络连接到网关之前，必须先创建要连接的虚拟网络的网关子网。 网关服务使用网关子网中指定的 IP 地址。

在[门户](http://portal.azure.com/)中，导航到要为其创建虚拟网关的 Resource Manager 虚拟网络。

1. 在**设置**部分你的 VNet 页上，选择**子网**以展开**子网**页。
2. 上**子网**页上，选择 **+ 网关子网**以打开**添加子网**页。

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. 子网的“名称”自动填充为值“GatewaySubnet”。 此值是必需的 Azure 能够识别为网关子网的子网。 调整自动填充**地址范围**值，以匹配你的配置要求，然后选择**确定**底部的页后，可以创建子网。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure 中创建虚拟网络网关堆栈

1. 在门户页面左侧，选择 + 并在搜索中输入虚拟网络网关。 在**结果**，找到并选择**虚拟网络网关**。
2. 在底部**虚拟网络网关**页上，选择**创建**。 这会打开“创建虚拟网关”页。
3. 上**创建虚拟网络网关**页上，指定的值为虚拟网络网关，如所述的示例值加上下面详细列出的其他值。
    - **SKU**： 基本
    - **虚拟网络**： 选择前面创建的虚拟网络。 它将自动选择前面创建的网关子网。 
    - **第一个 IP 配置**： 这是你的网关的公共 IP。 
        - 单击**创建网关 IP 配置**而这将占用你**选择公共 IP 地址**页。
        - 单击 **+ 创建新**以打开**创建公共 IP 地址**页。
        - 输入**名称**对公共 IP 地址。 将保留为 SKU**基本**，然后选择**确定**底部的此页后，可以保存所做的更改。

    > [!note]  
    > VPN 网关当前仅支持动态公共 IP 地址分配。 但这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

4. 验证设置。 
5. 单击“创建”开始创建 VPN 网关。 将会验证这些设置，并会在仪表板上看到“正在部署虚拟网络网关”磁贴。 创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

    创建网关后，即可在门户中查看虚拟网络，从而查看分配给网关的 IP 地址。 网关显示为连接的设备。 你可以选择已连接的设备 （虚拟网络网关） 以查看详细信息。
6. 重复这些步骤在你的 Azure 堆栈部署。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure 堆栈中创建本地网络网关

本地网络网关通常是指本地位置。 为站点指定依据 Azure 或 Azure 堆栈可以引用它，然后指定将创建一个连接的本地 VPN 设备的 IP 地址的名称。 此外还可指定 IP 地址前缀，以便通过 VPN 网关将其路由到 VPN 设备。 指定的地址前缀是位于本地网络的前缀。 如果之后本地网络发生了更改，或需要更改 VPN 设备的公共 IP 地址，可轻松更新这些值。

1. 在门户中，选择 **+ 创建资源**。
2. 在搜索框中，输入**本地网络网关**，然后按**Enter**搜索。 这会返回一个结果列表。 单击**本地网络网关**，然后选择**创建**按钮以打开**创建本地网络网关**页。
3. 上**创建本地网络网关页**，指定的值为本地网络网关，我们的示例值加上下面详细列出的其他值中所述。
    - **IP 地址**： 这是你想要连接到 Azure 或 Azure 堆栈的 VPN 设备的公共 IP 地址。 指定有效的公共 IP 地址。 IP 地址不能位于 NAT 后面，并且必须可让 Azure 访问。 如果目前没有 IP 地址，可以使用示例中显示的值，但是需要返回并将占位符 IP 地址替换为 VPN 设备的公共 IP 地址。 否则，Azure 不能连接。
    -  指的是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保此处所指定的范围没有与要连接到的其他网络的范围相重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 此处使用你自己的值，如果你想要连接到你的本地站点，不显示在示例中的值。
    - **配置 BGP 设置**： 仅当配置 BGP 时使用。 否则，不选择此项。
    - **订阅**： 验证是否显示正确的订阅。
    - **资源组**： 选择你想要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
    - **位置**： 选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。
4. 完成后指定的值的操作后，选择**创建**页后，可以创建本地网络网关底部的按钮。
5. 重复这些步骤在你的 Azure 堆栈部署。

## <a name="configure-your-connection"></a>配置你的连接

通过站点到站点连接连接到本地网络需要 VPN 设备。 在此步骤中，你可以配置 VPN 设备，称为连接。 配置你的连接时，需要以下条件：
    - 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
    - 虚拟网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要查找你使用 Azure 门户的 VPN 网关的公共 IP 地址，请导航到虚拟网络网关，然后选择你的网关的名称。
在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。
1. 在门户中，选择 **+ 创建资源**。
2. 在搜索框中，输入**连接**，然后按**Enter**搜索。 这会返回一个结果列表。 单击**连接**，然后选择创建按钮以打开**创建连接**页。
3. 上**创建连接**页上，配置你的连接的值。
     - 基础知识：
        1. **连接类型**： 选择站点到站点 (IPSec)。
        2. **资源组**: （选择你的测试资源组）
     - 设置：
        1. **虚拟网络网关**： 选择前面创建的虚拟网络网关。
        2. **本地网络网关**： 选择前面创建的本地网络网关。 
        3. **连接名称**： 这将自动填充来自两个网关的值。 
        4. **共享密钥**： 此处的值必须匹配要用于你的本地 VPN 设备的值。 此示例使用“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。
    - 剩下的“订阅”、“资源组”和“位置”值是固定的。
4. 单击“确定”以创建连接。 你将看到 flash 在屏幕上创建连接。
5. 你可以查看中的连接 * * 虚拟网络网关的连接页。 状态会从**未知**到**连接**，然后**Succeeded**。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
