---
title: 使用 Azure 虚拟网络服务终结点保护对 Azure Cosmos DB 帐户的访问 | Microsoft Docs
description: 本文档介绍设置 Azure Cosmos DB 虚拟网络服务终结点所要执行的步骤。
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: de52521824c146f63fb16e2690e2a24167ae2efe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333906"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>使用 Azure 虚拟网络服务终结点保护对 Azure Cosmos DB 帐户的访问

可将 Azure CosmosDB 帐户配置为仅允许从特定的 Azure 虚拟网络子网进行访问。 从虚拟网络及其子网为 Azure CosmosDB 启用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)可确保通过最佳的安全路由将流量传送到 Azure Cosmos DB。  

Azure Cosmos DB 是一种全球分布式多模型数据库服务。 可将 Azure Cosmos DB 帐户中的数据复制到多个区域。 使用虚拟网络服务终结点配置 Azure Cosmos DB 后，每个虚拟网络允许从属于特定子网的 IP 进行访问。 下图演示了已启用虚拟网络服务终结点的 Azure Cosmos DB：

![虚拟网络服务终结点体系结构](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

使用虚拟网络服务终结点配置 Azure Cosmos DB 帐户后，只能从指定的子网访问该帐户，所有公共/Internet 访问权限将被删除。 若要详细了解服务终结点，请参阅 Azure [虚拟网络服务终结点概述](../virtual-network/virtual-network-service-endpoints-overview.md)一文。

> [!NOTE]
> 目前可为 Azure Cosmos DB SQL API 或 Mongo API 帐户配置虚拟网络服务终结点。 不久之后，可为其他 API 和主权云（例如 Azure 德国版或 Azure 政府版）配置服务终结点。 如果为 Azure Cosmos DB 帐户配置了现有的 IP 防火墙，请记下防火墙配置，删除 IP 防火墙，然后配置服务终结点 ACL。 配置服务终结点后，可根据需要重新启用 IP 防火墙。

## <a name="configure-service-endpoint-by-using-azure-portal"></a>使用 Azure 门户配置服务终结点
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>为现有的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到要在其中为 Azure Cosmos DB 配置服务终结点的虚拟网络。 导航到“服务终结点”边栏选项卡，确保为“Azure.CosmosDB”服务终结点启用了虚拟网络的子网。  

   ![确认已启用服务终结点](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。  

3. 在启用虚拟网络服务终结点之前，请复制与 Azure Cosmos DB 帐户关联的 IP 防火墙信息，供将来使用。 配置服务终结点后，可以重新启用 IP 防火墙。  

4. 从设置菜单中选择“防火墙和虚拟网络”，然后选择允许从“选定的网络”进行访问。  

3. 若要授予对现有虚拟网络子网的访问权限，请在“虚拟网络”下面选择“添加现有的 Azure 虚拟网络”。  

4. 选择要从中添加 Azure 虚拟网络的**订阅**。 选择要向其提供 Azure Cosmos DB 帐户访问权限的 Azure **虚拟网络**和**子网**。 接下来选择“启用”，以便为“Microsoft.AzureCosmosDB”启用包含服务终结点的选定网络。 完成后，选择“添加”。  

   ![选择虚拟网络和子网](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > 如果之前没有为选定的 Azure 虚拟网络和子网配置 Azure Cosmos DB 服务终结点，可以在执行此操作的过程中进行配置。 启用访问权限最长需要 15 分钟才能完成。 在记下防火墙 ACL 的内容后禁用 IP 防火墙以便稍后重新启用它们，这一点非常重要。 

   ![已成功配置虚拟网络和子网](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

现在，你的 Azure Cosmos DB 帐户只允许来自此选定子网的流量。 如果之前已启用 IP 防火墙，请使用前面所述的信息重新启用它们。

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>为新的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。  

> [!NOTE]
> 如果为 Azure Cosmos DB 帐户配置了现有的 IP 防火墙，请记下防火墙配置，删除 IP 防火墙，然后启用服务终结点。 如果在未禁用防火墙的情况下启用服务终结点，则来自该 IP 范围的流量将丢失虚拟 IP 标识，然后会被丢弃并显示 IP 筛选器错误消息。 因此，为防止出现此错误，应始终禁用防火墙规则，将其复制，从子网启用服务终结点，并最终从 Cosmos DB 对子网执行 ACL。 配置服务终结点并添加 ACL 后，可根据需要重新启用 IP 防火墙。

2. 在启用虚拟网络服务终结点之前，请复制与 Azure Cosmos DB 帐户关联的 IP 防火墙信息，供将来使用。 配置服务终结点后，可以重新启用 IP 防火墙。  

3. 从设置菜单中选择“防火墙和 Azure 虚拟网络”，然后选择允许从“选定的网络”进行访问。  

4. 若要授予对新 Azure 虚拟网络的访问权限，请在“虚拟网络”下面选择“添加新虚拟网络”。  

5. 提供创建新虚拟网络所需的详细信息，然后选择“创建”。 随后将为启用的“Microsoft.AzureCosmosDB”创建包含服务终结点的子网。

   ![为新虚拟网络选择虚拟网络和子网](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>允许从 Azure 门户访问

为 Azure Cosmos DB 数据库帐户启用 Azure 虚拟网络服务终结点之后，默认会禁用从门户或其他 Azure 服务的访问。 系统会阻止从所配置子网外部的计算机访问你的 Azure Cosmos DB 数据库帐户，包括从门户进行访问。

![允许从门户访问](./media/vnet-service-endpoint/allow-access-from-portal.png)

如果 Azure Cosmos DB 帐户由其他 Azure 服务（例如 Azure 搜索）使用，或者从流分析或 Power BI 进行访问，请选中“允许 Azure 服务的访问”来允许这种访问。

为确保能够从门户访问 Azure Cosmos DB 指标，需要启用“允许 Azure 门户的访问”选项。 若要详细了解这些选项，请参阅[来自 Azure 门户的连接](firewall-support.md#connections-from-the-azure-portal)和[来自 Azure PaaS 服务的连接](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services)部分。 选择访问权限后，选择“保存”以保存设置。

## <a name="remove-a-virtual-network-or-subnet"></a>删除虚拟网络或子网 

1. 在“所有资源”边栏选项卡中，找到为其分配了服务终结点的 Azure Cosmos DB 帐户。  

2. 从设置菜单中选择“防火墙和虚拟网络”。  

3. 若要删除某个虚拟网络或子网规则，请选择虚拟网络或子网旁边的“...”，然后选择“删除”。

   ![删除虚拟网络](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  单击“保存”应用所做的更改。

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>使用 Azure PowerShell 配置服务终结点 

在 Azure PowerShell 中使用以下步骤配置 Azure Cosmos DB 帐户的服务终结点：  

1. 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  在为帐户启用服务终结点之前，请务必记下 IP 防火墙设置并彻底删除 IP 防火墙。


> [!NOTE]
> 如果为 Azure Cosmos DB 帐户配置了现有的 IP 防火墙，请记下防火墙配置，删除 IP 防火墙，然后启用服务终结点。 如果在未禁用防火墙的情况下启用服务终结点，则来自该 IP 范围的流量将丢失虚拟 IP 标识，然后会被丢弃并显示 IP 筛选器错误消息。 因此，为防止出现此错误，应始终禁用防火墙规则，将其复制，从子网启用服务终结点，并最终从 Cosmos DB 对子网执行 ACL。 配置服务终结点并添加 ACL 后，可根据需要重新启用 IP 防火墙。

2. 在启用虚拟网络服务终结点之前，请复制与 Azure Cosmos DB 帐户关联的 IP 防火墙信息，供将来使用。 配置服务终结点后，请重新启用 IP 防火墙。  

3. 为虚拟网络的现有子网启用服务终结点。  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. 确保已在虚拟网络和子网中为 Azure Cosmos DB 启用了服务终结点，为在 CosmosDB 帐户中启用 ACL 做好准备。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. 运行以下 cmdlet 获取 Azure Cosmos DB 帐户的属性：  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. 初始化变量以供稍后使用。 设置现有帐户定义中的所有变量，如果有多个位置，需要将这些位置添加为数组的一部分。 此步骤还会通过将“accountVNETFilterEnabled”变量设置为“True”来配置虚拟网络服务终结点。 稍后要将此值分配到“isVirtualNetworkFilterEnabled”参数。  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. 运行以下 cmdlet，使用新配置更新 Azure Cosmos DB 帐户属性： 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. 运行以下命令，验证是否已使用上一步骤中配置的虚拟网络服务终结点更新 Azure Cosmos DB 帐户：

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>为已启用 IP 防火墙的 Azure Cosmos DB 帐户添加虚拟网络服务终结点

1. 首先请禁用 IP 防火墙访问 Azure Cosmos DB 帐户。  

2. 使用前面部分中所述的方法之一启用 Azure Cosmos DB 帐户的虚拟网络终结点。  

3. 重新启用 IP 防火墙访问。 

## <a name="test-the-access"></a>测试访问

若要检查是否按预期方式配置了 Azure Cosmos DB 的服务终结点，请使用以下步骤：

* 在虚拟网络中设置充当前端和后端的两个子网，为后端子网启用 Cosmos DB 服务终结点。  

* 在后端子网的 Cosmos DB 帐户中启用访问。  

* 创建两个虚拟机 - 一个位于后端子网中，另一个位于前端子网中。  

* 尝试从两个虚拟机访问 Azure Cosmos DB 帐户。 应该能够从后端子网中创建的虚拟机建立连接，但无法从在前端子网中创建的虚拟机建立连接。 尝试从前端子网建立连接时，请求将会出错并返回 404 代码，这表明已使用虚拟网络服务终结点保护了对 Azure Cosmos DB 的访问。 后端子网中的计算机能够正常工作。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>访问已启用虚拟网络访问控制列表 (ACL) 的 Azure Cosmos DB 帐户时会发生什么情况？  

将返回 HTTP 404 错误。  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>是否允许不同区域中创建的虚拟网络子网访问另一个区域中的 Azure Cosmos DB 帐户？ 例如，如果 Azure Cosmos DB 帐户位于美国西部或美国东部，而虚拟网络位于多个区域，该虚拟网络是否可以访问 Azure Cosmos DB？  

是的，在不同区域中创建的虚拟网络可以通过新功能进行访问。 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Azure Cosmos DB 帐户是否可以同时包含虚拟网络服务终结点和防火墙？  

是的，虚拟网络服务终结点和防火墙可以共存。 一般情况下，在配置虚拟网络服务终结点之前，应确保始终启用门户访问，以便能够查看与容器关联的指标。

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>为 Azure Cosmos DB 启用服务终结点访问后，是否可以“允许给定 Azure 区域中其他 Azure 服务的访问”？  

仅当你希望自己的 Azure Cosmos DB 帐户可供其他 Azure 第一方服务（例如 Azure 数据工厂和 Azure 搜索）或给定 Azure 区域中部署的任何服务访问时，才需要这样做。

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Azure Cosmos DB 允许多少个虚拟网络服务终结点？  

一个 Azure Cosmos DB 帐户允许 64 个虚拟网络服务终结点。

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>服务终结点与网络安全组 (NSG) 规则之间存在怎样的关系？  

Azure Cosmos DB 中的 NSG 规则允许你限制对特定 Azure Cosmos DB IP 地址范围的访问。 如果希望允许访问特定[区域](https://azure.microsoft.com/global-infrastructure/regions/)中的 Azure Cosmos DB 实例，可以按照以下格式指定该区域： 

    AzureCosmosDB.<region name>

若要详细了解 NSG 标记，请参阅[虚拟网络服务标记](../virtual-network/security-overview.md#service-tags)一文。 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>IP 防火墙与虚拟网络服务终结点功能之间存在怎样的关系？  

这两个功能互为补充，确保 Azure Cosmos DB 资产的隔离并对其进行保护。 使用 IP 防火墙可确保静态 IP 能够访问 Azure Cosmos DB 帐户。  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>通过 Azure 虚拟网络网关 (VPN) 或 ExpressRoute 网关连接的本地设备的 IP 地址是否可以访问 Azure Cosmos DB 帐户？  

应将本地设备的 IP 地址或 IP 地址范围添加到静态 IP 列表，这样它们才能访问 Azure Cosmos DB 帐户。  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>如果删除一个已在其中为 Azure Cosmos DB 设置了服务终结点的虚拟网络，会发生什么情况？  

删除虚拟网络时，将会删除与该 Azure Cosmos DB 关联的 ACL 信息，并且会删除虚拟网络与 Azure Cosmos DB 帐户之间的交互。 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>如果删除一个已启用虚拟网络服务终结点的 Azure Cosmos DB 帐户，会发生什么情况？

将从子网中删除与特定 Azure Cosmos DB 帐户关联的元数据。 最终用户需负责删除所用的子网和虚拟网络。

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>是否可以使用对等互连的虚拟网络为 Azure Cosmos DB 创建服务终结点？  

不可以，只有直连虚拟网络及其子网才能创建 Azure Cosmos DB 服务终结点。

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>已启用 Azure Cosmos DB 服务终结点的子网中的资源（例如虚拟机）的源 IP 地址会发生什么情况？

启用虚拟网络服务终结点后，虚拟网络子网中资源的源 IP 地址将从公共 IPV4 地址改为使用 Azure 虚拟网络的专用地址，以便将流量传送到 Azure Cosmos DB。

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB 是否驻留在客户提供的 Azure 虚拟网络中？  

Azure Cosmos DB 是使用公共 IP 地址的多租户服务。 使用服务终结点功能限制对 Azure 虚拟网络子网的访问时，将会通过给定 Azure 虚拟网络及其子网来限制对 Azure Cosmos DB 帐户的访问。  Azure Cosmos DB 帐户不会驻留在该 Azure 虚拟网络中。 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>如果已启用日志记录，如何在 Log Analytics/OMS 中记录任何信息？  

对于 ACL 阻止的请求，Azure Cosmos DB 将会推送包含 IP 地址（不包括最后一个八位字节）和状态 403 的日志。  

## <a name="next-steps"></a>后续步骤
若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](firewall-support.md)一文。

