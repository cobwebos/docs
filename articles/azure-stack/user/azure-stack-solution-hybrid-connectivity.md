---
title: 使用 Azure 和 Azure Stack 配置混合云连接 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 配置混合云连接。
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
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605189"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>教程：使用 Azure 和 Azure Stack 配置混合云连接

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用混合连接模式在公有云 Azure 和 Azure Stack 中安全地访问资源。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 保留在本地的数据以满足隐私或法规要求，但有权访问全局 Azure 资源。
> - 在公有云 Azure 中使用云规模的应用部署和资源的同时，保留旧版系统。

## <a name="prerequisites"></a>必备组件

生成混合连接部署所需几个组件。 某些这些组件将需要时间来准备，，因此，你将需要相应地进行规划。

**Azure Stack**

Azure OEM/硬件合作伙伴可以部署生产 Azure 堆栈和所有用户就可以都部署 Azure 堆栈开发工具包 (ASDK)。

**Azure 堆栈组件**

Azure 堆栈运算符必须部署应用程序服务、 创建计划，并提供、 创建租户订阅，并添加 Windows Server 2016 映像。 如果你已有的某些这些组件，请确保在开始本教程前满足要求。

本教程假定你具有 Azure 和 Azure 堆栈的一些基本知识。 若要了解详细信息在开始本教程之前，请阅读以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
 - 在 Azure 中创建 [Web 应用](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal)。 记下的 Web 应用 URL，因为你将在本教程需要它。

### <a name="azure-stack"></a>Azure Stack

 - 使用您的生产 Azure 堆栈或部署 Azure 堆栈开发工具包从https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1。
   >[!Note]
   >部署 ASDK 可以需要长达 7 小时，因此请相应地计划。

 - 将[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服务部署到 Azure Stack。
 - [创建计划和提供](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)Azure 堆栈环境中。
 - 在 Azure Stack 环境中[创建租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。

### <a name="before-you-begin"></a>开始之前

验证你在开始配置混合云连接之前满足以下条件：

 - 你需要为你的 VPN 设备的面向外部的公共 IPv4 地址。 此 IP 地址不能位于 nat 之后。
 - 所有资源都部署在每个位置的区域相同。

#### <a name="tutorial-example-values"></a>教程的示例值

在本教程中的示例使用以下值。 这些值可用于创建测试环境或以更好地了解示例引用它们。 有关通用 VPN 网关设置的详细信息，请参阅[关于 VPN 网关设置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

连接规范：

 - **VPN 类型**：基于路由
 - **连接类型**：站点到站点 (IPsec)
 - **网关类型**：VPN
 - **Azure 连接名称**：Azure-Gateway-AzureStack-S2SGateway（门户会自动填充此值）
 - **Azure Stack 连接名称**：AzureStack-Gateway-Azure-S2SGateway（门户会自动填充此值）
 - **共享密钥**：任何与 VPN 硬件兼容且在连接两端的值匹配的共享密钥
 - **订阅**：任何首选订阅
 - **资源组**：Test-Infra

网络和子网的 IP 地址：

| Azure/Azure Stack 连接 | 名称 | 子网 | IP 地址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虚拟网关 | Azure-Gateway |  |  |
| Azure Stack 虚拟网关 | AzureStack-Gateway |  |  |
| Azure 公共 IP | Azure-GatewayPublicIP |  | 在创建时确定 |
| Azure Stack 公共 IP | AzureStack-GatewayPublicIP |  | 在创建时确定 |
| Azure 本地网关 | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 公共 IP 值 |
| Azure Stack 本地网关 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 公共 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>在公有云 Azure 和 Azure Stack 中创建虚拟网络

使用以下步骤以使用门户创建虚拟网络。 你可以使用这些[示例值](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)如果你为教程，说明如何使用这篇文章。 但是，如果你要使用本文配置生产环境，则会将示例设置替换你自己的值。

> [!IMPORTANT]
> 你必须确保不存在的 IP 地址在 Azure 或 Azure 堆栈 vNet 地址空间重叠。

在 Azure 中创建 vNet:

1. 使用你的浏览器连接到[Azure 门户](http://portal.azure.com/)并使用你的 Azure 帐户登录。
2. 选择**创建资源**。 在“搜索 Marketplace”字段中输入 `virtual network`。 查找**虚拟网络**在结果列表中，，然后选择**虚拟网络**。
3. 从**选择部署模型**列表中，选择**资源管理器**，然后选择**创建**。
4. 上**创建虚拟网络**，配置 VNet 设置。 必填的字段名称以红色星号作为前缀。  当输入有效的值时，星号将更改为一个绿色复选标记。

若要在 Azure 堆栈中创建 vNet:

* 重复前面的步骤 (1-4) 使用 Azure 堆栈**租户门户**。

## <a name="add-a-gateway-subnet"></a>添加网关子网

在连接到网关的虚拟网络之前, 你需要创建你想要连接到虚拟网络的网关子网。 网关服务使用你在网关子网中指定的 IP 地址。

在[Azure 门户](http://portal.azure.com/)，导航到你想要创建虚拟网络网关资源管理器虚拟网络。

1. 选择 vNet 以打开**虚拟网络**页。
2. 在**设置**，选择**子网**。
3. 在“子网”页中选择“+网关子网”，打开“添加子网”页。

    ![添加网关子网](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **名称**为子网自动填充使用 GatewaySubnet 的值。 Azure 需要此值才能识别作为网关子网的子网。
5. 更改**地址范围**提供以匹配你的配置要求，然后选择的值**确定**。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建虚拟网关

使用以下步骤在 Azure 中创建虚拟网络网关。

1. 在门户页面左侧，选择**+** 并在搜索字段中输入虚拟网络网关。
2. 在**结果**，选择**虚拟网络网关**。
3. 在**虚拟网络网关**，选择**创建**以打开**创建虚拟网络网关**页。
4. 上**创建虚拟网络网关**，为指定值与网络网关中, 所示**教程示例值**，和以下的其他值：

    - **SKU**：基本
    - **虚拟网络**：选择此前创建的虚拟网络。 自动选择你创建的网关子网。
    - **首个 IP 配置**：这是网关的公共 IP。
        - 选择**创建网关 IP 配置**，随即将跳转到**选择公共 IP 地址**页。
        - 选择 **+ 创建新**以打开**创建公共 IP 地址**页。
        - 输入公共 IP 地址的“名称”。 将保留为 SKU**基本**，然后选择**确定**以保存所做的更改。

       > [!Note]
       > 目前，VPN 网关仅支持动态公共 IP 地址分配。 但是，这并不意味着，分配给 VPN 网关后，IP 地址发生更改。 公共 IP 地址只在删除或重新创建网关时更改。 调整大小、 重置，或其他内部维护/升级你的 VPN 网关不更改的 IP 地址。

4. 验证你的网关设置。
5. 选择**创建**创建 VPN 网关。 验证网关设置，并在你的仪表板上显示"部署虚拟网络网关"磁贴。

   >[!Note]
   >创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

    创建网关后，你可以看到通过查看门户中的虚拟网络分配给它的 IP 地址。 网关显示为连接的设备。 若要查看有关网关的详细信息，请选择的设备。

6. 重复前面的步骤 (1-5) 在你的 Azure 堆栈部署。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建本地网关

本地网络网关通常是指本地位置。 提供给网站的名称，Azure 或 Azure 堆栈可以引用，然后指定：

- 你要创建的连接的本地 VPN 设备 IP 地址。
- 将路由通过 VPN 网关到 VPN 设备 IP 地址前缀。 指定的地址前缀是位于本地网络的前缀。

  >[!Note]
  >如果你在本地网络更改，或需要更改 VPN 设备的公共 IP 地址，你可以轻松更新这些值更高版本。

1. 在门户中选择“+创建资源”。
2. 在搜索框中，输入**本地网络网关**，然后选择**Enter**搜索。 这会返回一个结果列表。
3. 选择**本地网络网关**，然后选择**创建**以打开**创建本地网络网关**页。
4. 上**创建本地网络网关**，指定的值为本地网络网关，使用我们**教程示例值**。 包括以下的其他值。

    - **IP 地址**：这是需要 Azure 或 Azure Stack 连接到的 VPN 设备的公共 IP 地址。 指定有效的公共 IP 地址，以便 Azure 可以到达该地址不在 NAT 之后。 如果你现在没有 IP 地址，你可以使用此示例中的一个值用作占位符，但你将需要回过头来将占位符替换为你的 VPN 设备的公共 IP 地址。 Azure 无法连接到设备，直至你提供有效的地址。
    - **地址空间**： 这是此本地网络代表网络的地址范围。 可以添加多个地址空间范围。 请确保你指定的范围不与你想要连接到其他网络的范围重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果你想要连接到你在本地站点，而不是一个示例值，请使用你自己的值。
    - **配置 BGP 设置**：仅在配置 BGP 时使用。 否则，不选择此项。
    - **订阅**：确保显示的是正确订阅。
    - **资源组**：选择要使用的资源组。 你可以创建新的资源组，或选择一个已创建。
    - **位置**：选择将在其中创建此对象的位置。 你可能想要选择你的 VNet 位于相同位置，但你不需要这样做。
5. 当你完成指定所需的值时，选择**创建**创建本地网络网关。
6. 上重复这些步骤 (1-5) 你 Azure 堆栈的部署。

## <a name="configure-your-connection"></a>配置连接

通过站点到站点连接连接到本地网络需要 VPN 设备。 你配置 VPN 设备称为连接。 若要配置你的连接，你需要：

- 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
- 虚拟网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 Azure 门户查找 VPN 网关的公共 IP 地址，请导航到“虚拟网关”，然后选择网关的名称。

使用以下步骤创建虚拟网络网关和本地 VPN 设备之间的站点到站点 VPN 连接。

1. 在 Azure 门户中，选择 **+ 创建资源**。
2. 搜索**连接**。
3. 在**结果**，选择**连接**。
4. 上**连接**，选择**创建**。
5. 上**创建连接**，配置下列设置：

    - **连接类型**：选择“站点到站点(IPSec)”。
    - **资源组**： 选择你的测试资源组。
    - **虚拟网络网关**： 选择你创建的虚拟网络网关。
    - **本地网络网关**： 选择你创建的本地网络网关。
    - **连接名称**： 这自动填充使用从两个网关的值。
    - **共享密钥**： 此值必须匹配要用于你的本地 VPN 设备的值。 教程的示例使用 abc123，但你可以 （并且应该） 在使用更复杂。 重要的是，此值必须指定配置 VPN 设备时的相同值。
    - “订阅”、“资源组”和“位置”值是固定的。

6. 选择**确定**创建你的连接。

你可以看到中的连接**连接**虚拟网络网关的页。 “状态”会从“未知”转换为“正在连接”，再转换为“成功”。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
