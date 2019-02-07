---
title: 为 Azure Cosmos DB 帐户配置基于虚拟网络和子网的访问
description: 本文档介绍为 Azure Cosmos DB 设置虚拟网络服务终结点所要执行的步骤。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 4511ef81ccfa7008cd21a22b09459fb122d46327
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452934"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>配置从虚拟网络 (VNet) 访问

可将 Azure Cosmos DB 帐户配置为仅允许从 Azure 虚拟网络的特定子网进行访问。 限制使用虚拟网络中子网的连接对 Azure Cosmos DB 帐户的访问：
 
1. 让该子网将其标识和虚拟网络标识发送到 Azure Cosmos DB。 在特定的子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。

1. 在 Azure Cosmos DB 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

> [!NOTE]
> 在子网中为 Azure Cosmos DB 帐户启用服务终结点之后，抵达 Azure Cosmos DB 的流量的源将从公共 IP 切换到虚拟网络和子网。 流量切换适用于从此子网访问的任何 Azure Cosmos DB 帐户。 如果 Azure Cosmos DB 帐户包含允许此子网的基于 IP 的防火墙，则已启用服务的子网发出的请求将不再与 IP 防火墙规则相匹配，因此会遭到拒绝。 
>
> 有关详细信息，请参阅本文的[从 IP 防火墙规则迁移到虚拟网络访问控制列表](#migrate-from-firewall-to-vnet)部分所述的步骤。 

以下部分介绍如何为 Azure Cosmos DB 帐户配置虚拟网络服务终结点。

## <a id="configure-using-portal"></a>使用 Azure 门户配置服务终结点

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>为现有的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。

1. 从设置菜单中选择“防火墙和虚拟网络”，然后选择允许从“选定的网络”进行访问。

1. 若要授予对现有虚拟网络子网的访问权限，请在“虚拟网络”下面选择“添加现有的 Azure 虚拟网络”。

1. 选择要从中添加 Azure 虚拟网络的**订阅**。 选择要向其提供 Azure Cosmos DB 帐户访问权限的 Azure **虚拟网络**和**子网**。 接下来选择“启用”，以便为“Microsoft.AzureCosmosDB”启用包含服务终结点的选定网络。 完成后，选择“添加”。 

   ![选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. 允许从虚拟网络访问 Azure Cosmos DB 帐户之后，只允许来自此所选子网的流量。 添加的虚拟网络和子网应会显示，如以下屏幕截图所示：

   ![已成功配置虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 若要启用虚拟网络服务终结点，需要以下订阅权限：
  * 使用虚拟网络的订阅：网络参与者
  * 使用 Azure Cosmos DB 帐户的订阅：DocumentDB 帐户参与者

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>为新的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。  

1. 从设置菜单中选择“防火墙和 Azure 虚拟网络”，然后选择允许从“选定的网络”进行访问。  

1. 若要授予对新 Azure 虚拟网络的访问权限，请在“虚拟网络”下面选择“添加新虚拟网络”。  

1. 提供创建新虚拟网络所需的详细信息，然后选择“创建”。 随后将为启用的“Microsoft.AzureCosmosDB”创建包含服务终结点的子网。

   ![为新虚拟网络选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

如果 Azure Cosmos DB 帐户由其他 Azure 服务（例如 Azure 搜索）使用，或者从流分析或 Power BI 进行访问，请选择“接受来自公共 Azure 数据中心内部的连接”来允许这种访问。

为确保能够从门户访问 Azure Cosmos DB 指标，需要启用“允许从 Azure 门户访问”选项。 有关这些选项的详细信息，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)一文。 启用访问后，选择“保存”以保存设置。

## <a id="remove-vnet-or-subnet"></a>删除虚拟网络或子网 

1. 在“所有资源”边栏选项卡中，找到为其分配了服务终结点的 Azure Cosmos DB 帐户。  

2. 从设置菜单中选择“防火墙和虚拟网络”。  

3. 若要删除某个虚拟网络或子网规则，请选择虚拟网络或子网旁边的“...”，然后选择“删除”。

   ![删除虚拟网络](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  单击“保存”应用所做的更改。

## <a id="configure-using-powershell"></a>使用 Azure PowerShell 配置服务终结点

> [!NOTE]
> 使用 PowerShell 或 Azure CLI 时，请务必在参数中指定 IP 筛选器和虚拟网络 ACL 的完整列表，而不仅仅是需要添加的项。

在 Azure PowerShell 中使用以下步骤配置 Azure Cosmos DB 帐户的服务终结点：  

1. 安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  

1. 为虚拟网络的现有子网启用服务终结点。  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. 获取虚拟网络信息。

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. 运行以下 cmdlet 获取 Azure Cosmos DB 帐户的属性：  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化变量以供稍后使用。 设置现有帐户定义中的所有变量。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 运行以下 cmdlet，使用新配置更新 Azure Cosmos DB 帐户属性： 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
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

1. 使用子网访问控制列表 (ACL) 更新现有的 Azure Cosmos DB 帐户。

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. 使用子网 ACL 创建新的 Azure Cosmos DB 帐户

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

## <a id="migrate-from-firewall-to-vnet"></a>从 IP 防火墙规则迁移到虚拟网络 ACL 

仅当你要使用基于虚拟网络和子网的 ACL 而不是 IP 防火墙规则时，才需要对包含允许子网的现有 IP 防火墙规则的 Azure Cosmos DB 帐户执行以下步骤。

为子网启用 Azure Cosmos DB 帐户的服务终结点后，将使用包含虚拟网络和子网信息而不是公共 IP 的源发送请求。 这些请求与 IP 筛选器不匹配。 从启用了服务终结点的子网访问的所有 Azure Cosmos DB 帐户会发生这种源切换。 为防止停机，请使用以下步骤：

1. 运行以下 cmdlet 获取 Azure Cosmos DB 帐户的属性：

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 初始化变量供稍后使用。 设置现有帐户定义中的所有变量。 将所有虚拟网络 ACL 添加到从具有 `ignoreMissingVNetServiceEndpoint` 标志的子网访问的所有 Azure Cosmos DB 帐户。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 运行以下 cmdlet，使用新配置更新 Azure Cosmos DB 帐户属性：

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. 针对从子网访问的所有 Azure Cosmos DB 帐户重复步骤 1-3。

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
