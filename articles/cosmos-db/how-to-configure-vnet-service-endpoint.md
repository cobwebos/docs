---
title: 如何为 Azure Cosmos 帐户配置基于虚拟网络和子网的访问
description: 本文档介绍设置 Azure Cosmos DB 虚拟网络服务终结点所要执行的步骤。
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633676"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>如何从虚拟网络访问 Azure Cosmos DB 资源

可将 Azure CosmosDB 帐户配置为仅允许从特定的 Azure 虚拟网络子网进行访问。 需要执行两个步骤才能限制使用虚拟网络 (VNET) 中子网的连接对 Azure Cosmos 帐户进行访问。
 
1. 让该子网将其标识和 VNET 标识发送到 Azure Cosmos DB。 在特定的子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。

1. 在 Azure Cosmos 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

> [!NOTE]
> 在子网中为 Azure Cosmos 帐户启用服务终结点之后，抵达 Azure Cosmos DB 的流量的源将从公共 IP 切换到 VNET 和子网。 流量切换适用于从此子网访问的任何 Azure Cosmos 帐户。 如果 Azure Cosmos 帐户包含允许此子网的基于 IP 的防火墙，则已启用服务的子网发出的请求将不再与 IP 防火墙规则相匹配，因此会遭到拒绝。 有关详细信息，请参阅本文的[从 IP 防火墙规则迁移到 VNET 访问控制列表](#migrate-from-firewall-to-vnet)部分所述的步骤。 

以下部分介绍如何为 Azure Cosmos DB 帐户配置 VNET 服务终结点。

## <a id="configure-using-portal"></a>使用 Azure 门户配置服务终结点

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>为现有的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos 帐户。

1. 从设置菜单中选择“防火墙和虚拟网络”，然后选择允许从“选定的网络”进行访问。

1. 若要授予对现有虚拟网络子网的访问权限，请在“虚拟网络”下面选择“添加现有的 Azure 虚拟网络”。

1. 选择要从中添加 Azure 虚拟网络的**订阅**。 选择要向其提供 Azure Cosmos 帐户访问权限的 Azure **虚拟网络**和**子网**。 接下来选择“启用”，以便为“Microsoft.AzureCosmosDB”启用包含服务终结点的选定网络。 完成后，选择“添加”。 

   ![选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. 允许从虚拟网络访问 Azure Cosmos 帐户之后，只允许来自此所选子网的流量。 添加的虚拟网络和子网应会显示，如以下屏幕截图所示：

   ![已成功配置虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 若要启用虚拟网络服务终结点，需要以下订阅权限：
  * 使用 VNET 的订阅：网络参与者
  * 使用 Azure Cosmos 帐户的订阅：DocumentDB 帐户参与者

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>为新的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。  

2. 在启用虚拟网络服务终结点之前，请复制与 Azure Cosmos DB 帐户关联的 IP 防火墙信息，供将来使用。 配置服务终结点后，可以重新启用 IP 防火墙。  

3. 从设置菜单中选择“防火墙和 Azure 虚拟网络”，然后选择允许从“选定的网络”进行访问。  

4. 若要授予对新 Azure 虚拟网络的访问权限，请在“虚拟网络”下面选择“添加新虚拟网络”。  

5. 提供创建新虚拟网络所需的详细信息，然后选择“创建”。 随后将为启用的“Microsoft.AzureCosmosDB”创建包含服务终结点的子网。

   ![为新虚拟网络选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

如果 Azure Cosmos 帐户由其他 Azure 服务（例如 Azure 搜索）使用，或者从流分析或 Power BI 进行访问，请选中“允许 Azure 服务的访问”来允许这种访问。

为确保能够从门户访问 Azure Cosmos DB 指标，需要启用“允许 Azure 门户的访问”选项。 若要详细了解这些选项，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)一文的“从 Azure 门户请求”和“从 Azure PaaS 服务请求”部分。 选择访问权限后，选择“保存”以保存设置。

## <a id="remove-vnet-or-subnet"></a>删除虚拟网络或子网 

1. 在“所有资源”边栏选项卡中，找到为其分配了服务终结点的 Azure Cosmos DB 帐户。  

2. 从设置菜单中选择“防火墙和虚拟网络”。  

3. 若要删除某个虚拟网络或子网规则，请选择虚拟网络或子网旁边的“...”，然后选择“删除”。

   ![删除虚拟网络](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  单击“保存”应用所做的更改。

## <a id="configure-using-powershell"></a>使用 Azure PowerShell 配置服务终结点 

在 Azure PowerShell 中使用以下步骤配置 Azure Cosmos DB 帐户的服务终结点：  

1. 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  

1. 为虚拟网络的现有子网启用服务终结点。  

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

1. 确保已在虚拟网络和子网中为帐户启用了服务终结点，为在 Azure Cosmos 帐户中启用 ACL 做好准备。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. 运行以下 cmdlet 获取 Azure Cosmos DB 帐户的属性：  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化变量以供稍后使用。 设置现有帐户定义中的所有变量，如果有多个位置，需要将这些位置添加为数组的一部分。 此步骤还会通过将“accountVNETFilterEnabled”变量设置为“True”来配置虚拟网络服务终结点。 稍后要将此值分配到“isVirtualNetworkFilterEnabled”参数。 

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

1. 运行以下 cmdlet，使用新配置更新 Azure Cosmos DB 帐户属性： 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. 运行以下命令，验证是否已使用上一步骤中配置的虚拟网络服务终结点更新 Azure Cosmos DB 帐户：

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>使用 Azure CLI 配置服务终结点 

1. 为虚拟网络的子网启用服务终结点。

1. 使用子网 ACL 更新现有的 Azure Cosmos 帐户

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. 使用子网 ACL 创建新的 Azure Cosmos 帐户

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>从 IP 防火墙规则迁移到 VNET ACL 

仅当你要使用基于 VNET 和子网的 ACL 而不是 IP 防火墙规则时，才需要对包含允许子网的现有 IP 防火墙规则的 Azure Cosmos 帐户执行以下步骤。

为子网启用 Azure Cosmos 帐户的服务终结点后，将使用包含 VNET 和子网信息而不是公共 IP 的源发送请求。 因此，此类请求与 IP 筛选器不匹配。 从启用了服务终结点的子网访问的所有 Azure Cosmos 帐户会发生这种源切换。 为防止停机，请使用以下步骤：

1. 运行以下 cmdlet 获取 Azure Cosmos 帐户的属性：

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化变量供稍后使用。 设置现有帐户定义中的所有变量。 将所有 VNET ACL 添加到从具有 `ignoreMissingVNetServiceEndpoint` 标志的子网访问的所有 Azure Cosmos 帐户。  

   如果有多个位置，需将其添加为数组的一部分。 此步骤还会通过将“accountVNETFilterEnabled”变量设置为“True”来配置虚拟网络服务终结点。 稍后要将此值分配到“isVirtualNetworkFilterEnabled”参数。

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
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. 运行以下 cmdlet，使用新配置更新 Azure Cosmos 帐户属性：

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. 针对从子网访问的所有 Azure Cosmos 帐户重复步骤 1-3。

1.  等待 15 分钟，然后更新子网以启用服务终结点。

1.  为虚拟网络的现有子网启用服务终结点。

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. 删除子网的 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](firewall-support.md)一文。

