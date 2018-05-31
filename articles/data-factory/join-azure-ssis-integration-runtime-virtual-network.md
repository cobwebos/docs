---
title: 将 Azure-SSIS 集成运行时加入虚拟网络 | Microsoft Docs
description: 了解如何将 Azure-SSIS 集成运行时加入 Azure 虚拟网络。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: douglasl
ms.openlocfilehash: 2bb6491a470e7041568bb6b9183e996d2a9119d9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939902"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络
对于以下情况，请将 Azure-SSIS 集成运行时 (IR) 加入 Azure 虚拟网络： 

- 在属于虚拟网络的 Azure SQL 数据库托管实例（预览版）上承载 SQL Server Integration Services (SSIS) 目录数据库。
- 想要从 Azure-SSIS 集成运行时中运行的 SSIS 包连接到本地数据存储。

 使用 Azure 数据工厂版本 2（预览版）可将 Azure-SSIS 集成运行时加入通过经典部署模型或 Azure 资源管理器部署模型创建的虚拟网络。 

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-introduction.md)。

## <a name="access-to-on-premises-data-stores"></a>访问本地数据存储
如果 SSIS 包仅访问公有云数据存储，则不需要将 Azure-SSIS IR 加入虚拟网络。 如果 SSIS 包访问本地数据存储，则必须将 Azure-SSIS IR 加入已连接到本地网络的虚拟网络。 

下面是几个要点： 

- 如果已有现有的虚拟网络连接到本地网络，请先创建 Azure-SSIS 集成运行时要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)或[经典虚拟网络](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置从该虚拟网络到本地网络的站点到站点 [VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)或 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 连接。
- 如果已有现有的 Azure 资源管理器或经典虚拟网络连接到 Azure-SSIS 集成运行时所在同一位置中的本地网络，则可将 IR 加入该虚拟网络。
- 如果已有现有的经典虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的[经典虚拟网络](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到经典虚拟网络](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)连接。 也可以创建 Azure-SSIS 集成运行时要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md#create-a-virtual-network)。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。
- 如果已有现有的 Azure 资源管理器虚拟网络连接到与 Azure-SSIS IR 所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS IR 要加入到的 [Azure 资源管理器虚拟网络](../virtual-network/quick-create-portal.md##create-a-virtual-network)。 然后，配置 Azure 资源管理器到 Azure 资源管理器虚拟网络连接。 也可以创建 Azure-SSIS IR 要加入到的[经典虚拟网络](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到 Azure 资源管理器虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)连接。

## <a name="host-the-ssis-catalog-database-on-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例上承载 SSIS 目录数据库 
如果 SSIS 目录承载在虚拟网络上的 SQL 数据库托管实例（预览版）中，则可将 Azure-SSIS IR 加入：

- 相同的虚拟网络。
- 已与 SQL 数据库托管实例（预览版）所在的虚拟网络建立了网络到网络连接的不同虚拟网络中。 

可以通过经典部署模型或 Azure 资源管理器部署模型部署虚拟网络。 如果打算将 Azure-SSIS IR 加入到包含 SQL 数据库托管实例（预览版）的同一虚拟网络中，请确保 Azure-SSIS IR 与 SQL 数据库托管实例（预览版）在不同的子网中。   

以下部分提供了更多详细信息。

## <a name="requirements-for-virtual-network-configuration"></a>虚拟网络配置需求

-   请确保 `Microsoft.Batch` 是承载 Azure-SSIS IR 的 VNet 子网订阅下的已注册提供程序。 如果使用的是经典 VNet，也请将 `MicrosoftAzureBatch` 联接到该虚拟网络的经典虚拟机参与者角色。

-   选择合适的子网，承载 Azure-SSIS IR。 请参阅[选择子网](#subnet)。

-   如果要在虚拟网络上使用自己的域名服务 (DNS) 服务器，请参阅[域名服务服务器](#dns_server)。

-   如果要在子网上使用网络安全组 (NSG)，请参阅[网络安全组](#nsg)

-   如果要使用 Azure Express Route 或配置用户定义路由 (UDR)，请参阅[使用 Azure ExpressRoute 或用户定义路由](#route)。

-   请确保虚拟网络的资源组可以创建和删除特定 Azure 网络资源。 请参阅[资源组需求](#resource-group)。

### <a name="subnet"></a>选择子网
-   不要选择 GatewaySubnet 用于部署 Azure-SSIS 集成运行时，因为它专用于虚拟网络网关。

-   请确保选择的子网具有足够的可用地址空间以供 Azure-SSIS IR 使用。 在可用 IP 地址中至少保留 2 个 * IR 节点编号。 Azure 会保留每个子网中的某些 IP 地址，但是这些地址不能使用。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)。

-   不要使用其他 Azure 服务（例如，SQL 数据库托管实例、应用服务）以独占方式占用的子网。

### <a name="dns_server"></a>域名服务服务器 
如果需要在 Azure-SSIS 集成运行时联接的虚拟网络中使用自己的域名服务 (DNS) 服务器，请确保它可解析公共 Azure 主机名（例如，Azure 存储 blob 名称 `<your storage account>.blob.core.windows.net`）。

建议执行以下步骤：

-   配置自定义 DNS 将请求转发到 Azure DNS。 你可以在自己的 DNS 服务器上将未解析的 DNS 记录转发到 Azure 递归解析程序 (168.63.129.16) 的 IP 地址。

-   为 VNet 将自定义 DNS 设置为主 DNS，将 Azure DNS 设置为辅助 DNS。 将 Azure 递归解析程序 (168.63.129.16) 的 IP 地址注册为辅助 DNS 服务器，以防自己的 DNS 服务器不可用。

有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

### <a name="nsg"></a>网络安全组
如果需要在 Azure-SSIS 集成运行时加入的虚拟网络中实现网络安全组 (NSG)，请允许通过以下端口传送入站/出站流量：

| 方向 | 传输协议 | Source | 源端口范围 | 目标 | 目标端口范围 | 注释 |
|---|---|---|---|---|---|---|
| 入站 | TCP | Internet | * | VirtualNetwork | 29876、29877（如果将 IR 加入 Azure 资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口来与虚拟网络中 Azure-SSIS 集成运行时的节点通信。 |
| 出站 | TCP | VirtualNetwork | * | Internet | 443 | 虚拟网络中 Azure-SSIS 集成运行时的节点使用此端口来访问 Azure 服务，例如 Azure 存储和 Azure 事件中心。 |
| 出站 | TCP | VirtualNetwork | * | Internet 或 SQL | 1433、11000-11999、14000-14999 | 虚拟网络中 Azure-SSIS 集成运行时的节点使用这些端口来访问 Azure SQL 数据库服务器承载的 SSISDB。 （不适用于 SQL 数据库托管实例（预览版）承载的 SSISDB。） |
||||||||

### <a name="route"></a>使用 Azure ExpressRoute 或用户定义路由

可以将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到虚拟网络基础结构，从而将其本地网络扩展到 Azure。 

常见配置是使用强制隧道（播发 BGP 路由 0.0.0.0/0 到 VNet），以强制 VNet 的入站 Internet 流量流向本地网络设备，以进行检查和记录。 该流量流使用独立的 Azure 数据工厂服务中断了 VNet 中 Azure-SSIS IR 之间的连接。 解决方法是在包含 Azure-SSIS IR 缓存的子网上定义一个或多个[用户定义的路由 (UDR)](../virtual-network/virtual-networks-udr-overview.md)。 UDR 定义了要遵循的子网特定路由，而不是 BGP 路由。

或者，可以定义用户定义路由 (UDR) 以强制从承载 Azure-SSIS IR 的子网到其他子网（承载作为防火墙或用于检查和日志记录的 DMZ 主机的虚拟网络设备）的出站 Internet 流量。

在这两种情况下，在承载 Azure-SSIS IR 的子网上应用下一个跃点类型为 Internet 的 0.0.0.0/0 路由，确保数据工厂服务和 Azure-SSIS IS IR 之间的通信成功。 

![添加路由](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

如果担心无法检查来自该子网的出站 Internet 流量，还可以在子网上添加 NSG 规则，将出站目标限制为 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)。

有关示例，请参阅[此 PowerShell 脚本](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c)。 必须每周运行脚本，使 Azure 数据中心 IP 地址列表保持最新。

### <a name="resource-group"></a>资源组需求
Azure-SSIS IR 需要在与 VNet 相同的资源组（包括 Azure 负载均衡器、Azure 公共 IP 地址和网络工作安全组）下创建特定网络资源。

-   请确保 VNet 所属的资源组或订阅上没有任何资源锁定。 如果配置只读锁定或删除锁定，启动和停止 IR 可能会失败或挂起。

-   请确保没有任何 Azure 策略会阻止在 VNet 所属的资源组或订阅下创建以下资源：
    -   Microsoft.Network/LoadBalancers
    -   Microsoft.Network/NetworkSecurityGroups
    -   Microsoft.Network/PublicIPAddresses

## <a name="azure-portal-data-factory-ui"></a>Azure 门户（数据工厂 UI）
本部分介绍如何使用 Azure 门户和数据工厂 UI 如何将现有的 Azure SSIS 运行时加入虚拟网络（经典或 Azure 资源管理器）。 首先，在将 Azure SSIS IR 加入虚拟网络之前，需要正确配置虚拟网络。 根据虚拟网络的类型（经典或 Azure 资源管理器）完成以下两个部分之一。 然后，继续参阅第三部分，将 Azure SSIS IR 加入虚拟网络。 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>使用门户配置 Azure 资源管理器虚拟网络
需要先配置虚拟网络，然后才能将 Azure-SSIS IR 加入该虚拟网络。

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com)。
3. 选择“更多服务”。 筛选并选择“虚拟网络”。
4. 在列表中筛选并选择自己的虚拟网络。 
5. 在“虚拟网络”页中选择“属性”。 
6. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。
7. 在左侧菜单中选择“子网”。 确保**可用地址**的数目大于 Azure-SSIS 集成运行时中的节点数。
8. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。）

   a. 在 Azure 门户上的左侧菜单中，选择“订阅”。

   b. 选择订阅。 
   
   c. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。     
      ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>使用门户配置经典虚拟网络
需要先配置虚拟网络，然后才能将 Azure-SSIS IR 加入该虚拟网络。

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com)。
3. 选择“更多服务”。 筛选并选择“虚拟网络(经典)”。
4. 在列表中筛选并选择自己的虚拟网络。 
5. 在“虚拟网络(经典)”页中选择“属性”。 

    ![经典虚拟网络资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 选择“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。
6. 在左侧菜单中选择“子网”。 确保**可用地址**的数目大于 Azure-SSIS 集成运行时中的节点数。

    ![虚拟网络中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 将 **MicrosoftAzureBatch** 加入虚拟网络的“经典虚拟机参与者”角色。

    a. 在左侧菜单中选择“访问控制(IAM)”，并在工具栏中选择“添加”。 
       ![“访问控制”和“添加”按钮](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. 在“添加权限”页中，为“角色”选择“经典虚拟机参与者”。 在“选择”框中粘贴 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然后从搜索结果列表中选择“Microsoft Azure Batch”。   
       ![“添加权限”页上的搜索结果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. 选择“保存”以保存设置并关闭页面。  
       ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. 确认参与者列表中出现了“Microsoft Azure Batch”。  
       ![确认 Azure Batch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。）

   a. 在 Azure 门户上的左侧菜单中，选择“订阅”。

   b. 选择订阅。

   c. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。     
      ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 Web 浏览器支持数据工厂 UI。
2. 在 [Azure 门户](https://portal.azure.com)中，选择左侧菜单中的“数据工厂”。 如果菜单中未显示“数据工厂”，请选择“更多服务”，然后在“智能 + 分析”部分选择“数据工厂”。 
    
   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. 在列表中选择包含 Azure SSIS 集成运行时的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和部署”磁贴。 单独的选项卡中会显示数据工厂 UI。 

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。 

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. 如果 Azure SSIS IR 正在运行，请在集成运行时列表的“操作”列中，选择与 Azure SSIS IR 对应的“停止”按钮。 只有先停止 IR 才能对其进行编辑。 

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 在集成运行时列表的“操作”列中，选择与 Azure SSIS IR 对应的“编辑”按钮。

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. 在“集成运行时安装”窗口的“常规设置”页中，选择“下一步”。 

   ![IR 安装的常规设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. 在“SQL 设置”页上输入管理员密码，然后选择“下一步”。

   ![IR 安装的 SQL Server 设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. 在“高级设置”页上执行以下操作： 

   a. 选中“选择 Azure-SSIS 集成运行时要加入的 VNet，并允许 Azure 服务配置 VNet 权限/设置”复选框。

   b. 对于“类型”，请指定虚拟网络是经典虚拟网络还是 Azure 资源管理器虚拟网络。 

   c. 对于“VNet 名称”，请选择自己的虚拟网络。

   d. 对于“子网名称”，请选择虚拟网络中的子网。

   e. 选择“更新”。 

   ![IR 安装的高级设置](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. 现在，可以在“操作”中使用与 Azure-SSIS IR 对应的“启动”按钮启动该 IR。 启动 Azure-SSIS IR 大约需要 20 分钟。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>配置虚拟网络
需要先配置虚拟网络，然后才能将 Azure-SSIS IR 加入该虚拟网络。 若要自动配置需加入虚拟网络的 Azure-SSIS 集成运行时的权限/设置，请添加以下脚本：

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>创建 Azure-SSIS IR 并将其加入虚拟网络
可以创建 Azure-SSIS IR，并将其加入虚拟网络。 有关完整的脚本和说明，请参阅[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md#azure-powershell)。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>将现有 Azure-SSIS IR 加入虚拟网络
[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)一文中的脚本显示如何在同一个脚本中创建 Azure-SSIS IR 并将其加入虚拟网络。 如果已有 Azure-SSIS IR，请执行以下步骤将其加入虚拟网络： 

1. 停止 Azure-SSIS IR。
2. 将 Azure-SSIS IR 配置为加入虚拟网络。 
3. 启动 Azure-SSIS IR。 

### <a name="define-the-variables"></a>定义变量

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>停止 Azure-SSIS IR
停止 Azure-SSIS 集成运行时，然后才能将其加入虚拟网络。 此命令释放该运行时的所有节点并停止计费：

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>为要加入的 Azure-SSIS IR 配置虚拟网络设置

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>配置 Azure-SSIS IR
若要将 Azure-SSIS 集成运行时配置为加入虚拟网络，请运行 `Set-AzureRmDataFactoryV2IntegrationRuntime` 命令： 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>启动 Azure-SSIS IR
若要启动 Azure-SSIS 集成运行时，请运行以下命令： 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
此命令需要 20 到 30 分钟才能完成。

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此文提供有关创建 Azure-SSIS IR 的分步说明。 其中使用 Azure SQL 数据库来承载 SSIS 目录。 
- [创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了教程的内容，提供了有关使用 Azure SQL 数据库托管实例（预览版）以及将 IR 加入虚拟网络的说明。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。 
