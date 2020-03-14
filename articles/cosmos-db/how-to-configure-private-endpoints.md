---
title: 为 Azure Cosmos 帐户配置 Azure 专用链接
description: 了解如何使用虚拟网络中的专用 IP 地址设置 Azure 专用链接来访问 Azure Cosmos 帐户。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 466f870f257ca4d93764cbfdb4208e8cf1f75553
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205047"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>为 Azure Cosmos 帐户配置 Azure 专用链接

使用 Azure 专用链接，可以通过专用终结点连接到 Azure Cosmos 帐户。 专用终结点是虚拟网络内子网中的一组专用 IP 地址。 然后，可以通过专用 IP 地址限制对 Azure Cosmos 帐户的访问。 当私有链接与受限制的 NSG 策略结合时，它有助于降低数据渗透的风险。 若要了解有关专用终结点的详细信息，请参阅[Azure 专用链接](../private-link/private-link-overview.md)一文。

私有链接允许用户从虚拟网络或任何对等互连虚拟网络访问 Azure Cosmos 帐户。 映射到专用链接的资源也可通过 VPN 或 Azure ExpressRoute 通过专用对等互连在本地访问。 

你可以使用 "自动" 或 "手动" 审批方法连接到使用 "专用" 链接配置的 Azure Cosmos 帐户。 若要了解详细信息，请参阅专用链接文档的 "[批准工作流](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)" 部分。 

本文介绍创建专用终结点的步骤。 它假定您使用的是自动批准方法。

> [!NOTE]
> 专用终结点支持目前在仅适用于网关连接模式的支持区域中已正式发布。 对于直接模式，它作为预览功能提供。

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>使用 Azure 门户创建专用终结点

使用以下步骤，通过 Azure 门户创建现有 Azure Cosmos 帐户的专用终结点：

1. 从 "**所有资源**" 窗格中，选择 Azure Cosmos 帐户。

1. 从 "设置" 列表中选择 "**专用终结点连接**"，然后选择 "**专用终结点**"：

   ![用于在 Azure 门户中创建专用终结点的选择](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. 在 "**创建专用终结点-基本**信息" 窗格中，输入或选择以下详细信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选一个择资源组。|
    | **实例详细信息** |  |
    | 名称 | 输入专用终结点的任意名称。 如果使用此名称，请创建一个唯一的名称。 |
    |区域| 选择要在其中部署专用链接的区域。 在虚拟网络所在的同一位置创建专用终结点。|
    |||
1. 选择 "**下一步：资源**"。
1. 在“创建专用终结点 - 资源”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择 **"连接到我的目录中的 Azure 资源"** 。 <br/><br/> 然后，可以选择其中一个资源来设置专用链接。 或者，你可以使用与你共享的资源 ID 或别名连接到其他人的资源。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择 " **AzureCosmosDB/databaseAccounts**"。 |
    | 资源 |选择你的 Azure Cosmos 帐户。 |
    |目标子资源 |选择要映射的 Azure Cosmos DB API 类型。 默认情况下，对于 SQL、MongoDB 和 Cassandra Api，这是唯一的选择。 对于 Gremlin 和表 Api，还可以选择**sql** ，因为这些 api 可与 sql API 进行互操作。 |
    |||

1. 选择**下一步：配置**。
1. 在 "**创建专用终结点-配置**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择虚拟网络。 |
    | 子网 | 选择子网。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 <br><br/> 若要与专用终结点建立私下连接，需要 DNS 记录。 建议将专用终结点与专用 DNS 区域集成。 你还可以使用自己的 DNS 服务器，或使用虚拟机上的主机文件创建 DNS 记录。 |
    |专用 DNS 区域 |选择**privatelink.documents.azure.com**。 <br><br/> 专用 DNS 区域是自动确定的。 不能使用 Azure 门户对其进行更改。|
    |||

1. 选择“查看 + 创建”。 在 "**查看**" 和 "创建" 页上，Azure 会验证你的配置。
1. 看到“验证通过”消息时，选择“创建”。

如果已批准 Azure Cosmos 帐户的专用链接，请在 Azure 门户中的 "**防火墙和虚拟网络**" 窗格中的 "**所有网络**" 选项不可用。

下表显示了不同的 Azure Cosmos 帐户 API 类型、支持的子资源与相应的专用区域名称之间的映射。 你还可以通过 SQL API 访问 Gremlin 和表 API 帐户，因此这些 Api 有两个条目。

|Azure Cosmos 帐户 API 类型  |支持的子资源（或组 Id） |专用区域名称  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|表    |    表     |   privatelink.table.cosmos.azure.com    |
|表     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>提取专用 IP 地址

预配专用终结点后，可以查询 IP 地址。 查看 Azure 门户中的 IP 地址：

1. 选择“所有资源”，
1. 搜索之前创建的专用终结点。 在这种情况下，它是**cdbPrivateEndpoint3**。
1. 选择 "**概览**" 选项卡以查看 DNS 设置和 IP 地址。

![Azure 门户中的专用 IP 地址](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

每个专用终结点创建多个 IP 地址：

* 一个用于 Azure Cosmos 帐户的全局（区域不可知）终结点
* 一个用于部署 Azure Cosmos 帐户的每个区域。

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>使用 Azure PowerShell 创建专用终结点

运行以下 PowerShell 脚本，为现有的 Azure Cosmos 帐户创建名为 "MyPrivateEndpoint" 的专用终结点。 将变量值替换为你的环境的详细信息。

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>将专用终结点与专用 DNS 区域集成

创建专用终结点后，可以使用以下 PowerShell 脚本将其与专用 DNS 区域集成：

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>提取专用 IP 地址

预配专用终结点后，可以使用以下 PowerShell 脚本查询 IP 地址和 FQDN 映射：

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>使用资源管理器模板创建专用终结点

可以通过在虚拟网络子网中创建专用终结点来设置专用链接。 可以通过使用 Azure 资源管理器模板来实现此目的。

使用以下代码创建名为 "PrivateEndpoint_template 资源管理器" 的模板。 此模板在现有虚拟网络中创建现有 Azure Cosmos SQL API 帐户的专用终结点。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**定义模板的参数文件**

为该模板创建一个参数文件，并将其命名为 "PrivateEndpoint_parameters json"。 将以下代码添加到参数文件：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**使用 PowerShell 脚本部署模板**

使用以下代码创建 PowerShell 脚本。 在运行该脚本之前，请将订阅 ID、资源组名称和其他变量值替换为你的环境的详细信息。

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

在 PowerShell 脚本中，`GroupId` 变量只能包含一个值。 该值是帐户的 API 类型。 允许的值为： `Sql`、`MongoDB`、`Cassandra`、`Gremlin`和 `Table`。 某些 Azure Cosmos 帐户类型可通过多个 Api 访问。 例如：

* 可以从 Gremlin 和 SQL API 帐户访问 Gremlin API 帐户。
* 可以从表和 SQL API 帐户访问表 API 帐户。

对于这些帐户，你必须为每个 API 类型创建一个专用终结点。 在 `GroupId` 数组中指定了相应的 API 类型。

成功部署模板后，可以看到类似于下图所示的输出。 如果正确设置了专用终结点，则 `Succeeded` `provisioningState` 值。

![资源管理器模板的部署输出](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

部署模板后，子网中会保留专用 IP 地址。 Azure Cosmos 帐户的防火墙规则配置为仅接受来自专用终结点的连接。

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>将专用终结点与专用 DNS 区域集成

使用以下代码创建名为 "PrivateZone_template 资源管理器" 的模板。 此模板为现有虚拟网络中的现有 Azure Cosmos SQL API 帐户创建专用 DNS 区域。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

使用以下代码创建名为 "PrivateZoneRecords_template 资源管理器" 的模板。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**定义模板的参数文件**

为模板创建以下两个参数文件。 创建 "PrivateZone_parameters json"。 替换为以下代码：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

创建 "PrivateZoneRecords_parameters json"。 替换为以下代码：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**使用 PowerShell 脚本部署模板**

使用以下代码创建 PowerShell 脚本。 在运行该脚本之前，请将订阅 ID、资源组名称和其他变量值替换为你的环境的详细信息。

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>配置自定义 DNS

应在创建专用终结点的子网中使用专用 DNS 区域。 配置终结点，以便将每个专用 IP 地址映射到 DNS 条目。 （请参阅前面所示响应中的 `fqdns` 属性。）

创建专用终结点时，可以将其与 Azure 中的专用 DNS 区域集成。 如果选择改为使用自定义 DNS 区域，则必须将其配置为为专用终结点保留的所有专用 IP 地址添加 DNS 记录。

## <a name="private-link-combined-with-firewall-rules"></a>与防火墙规则结合的专用链接

结合使用专用链接和防火墙规则时，可能会出现以下情况和结果：

* 如果未配置任何防火墙规则，则默认情况下，所有流量都可以访问 Azure Cosmos 帐户。

* 如果配置公共流量或服务终结点，并创建私有终结点，则会通过相应类型的防火墙规则授权不同类型的传入流量。

* 如果未配置任何公用流量或服务终结点，并且创建专用终结点，则只能通过专用终结点访问 Azure Cosmos 帐户。 如果未配置公共流量或服务终结点，则在拒绝或删除所有已批准的专用终结点后，该帐户将对整个网络开放。

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>添加或删除区域时更新专用终结点

若要在 Azure Cosmos 帐户中添加或删除区域，需要在该帐户中添加或删除 DNS 条目。 添加或删除区域后，可以更新子网的专用 DNS 区域，以反映已添加或已删除的 DNS 条目及其相应的专用 IP 地址。

例如，假设你在三个区域部署 Azure Cosmos 帐户： "美国西部"、"美国中部" 和 "西欧"。 为帐户创建专用终结点时，子网中会保留四个专用 Ip。 这三个区域中的每一个都有一个 IP，并且与全局/地区无关的终结点有一个 IP。

稍后，你可以将新区域（例如 "美国东部"）添加到 Azure Cosmos 帐户。 添加新区域后，需要将相应的 DNS 记录添加到专用 DNS 区域或自定义 DNS。

删除区域时，可以使用相同的步骤。 删除区域后，需要从专用 DNS 区域或自定义 DNS 中删除相应的 DNS 记录。

## <a name="current-limitations"></a>当前限制

使用 Azure Cosmos 帐户的私有链接时，有以下限制：

* 仅在特定区域提供对 Azure Cosmos 帐户和虚拟网络的专用链接支持。 有关支持的区域的列表，请参阅私有链接文章的 "[可用区域](../private-link/private-link-overview.md#availability)" 部分。 

  > [!NOTE]
  > 若要创建专用终结点，请确保虚拟网络和 Azure Cosmos 帐户都在支持的区域中。

* 使用直接模式连接将专用链接与 Azure Cosmos 帐户一起使用时，只能使用 TCP 协议。 目前尚不支持 HTTP 协议。

* 专用终结点支持目前在仅适用于网关连接模式的支持区域中已正式发布。 对于直接模式，它作为预览功能提供。

* 当你使用 Azure Cosmos DB 的适用于 MongoDB 帐户的 API 时，仅支持服务器版本3.6 上的帐户的专用终结点（即，以 `*.mongo.cosmos.azure.com`格式使用终结点的帐户）。 服务器版本3.2 上的帐户不支持专用链接（即，使用该终结点的帐户格式 `*.documents.azure.com`）。 若要使用专用链接，你应该将旧帐户迁移到新版本。

* 如果 Azure Cosmos DB 使用的是具有私有链接的 MongoDB 帐户的 API，则无法使用 Robo 3T、Studio 3T 和 Mongoose 等工具。 仅当指定了 `appName=<account name>` 参数时，终结点才能具有专用链接支持。 示例为 `replicaSet=globaldb&appName=mydbaccountname`。 由于这些工具不会在连接字符串中将应用名称传递到服务，因此不能使用 "专用链接"。 但你仍可以通过使用3.6 版本的 SDK 驱动程序来访问这些帐户。

* 如果虚拟网络包含 "专用" 链接，则无法移动或删除该网络。

* 如果 Azure Cosmos 帐户已附加到专用终结点，则无法将其删除。

* 无法将 Azure Cosmos 帐户故障转移到未映射到附加到该帐户的所有专用终结点的区域。

* 应至少向网络管理员授予 Azure Cosmos 帐户范围内的 "*/PrivateEndpointConnectionsApproval" 权限，才能创建自动批准的专用终结点。

### <a name="limitations-to-private-dns-zone-integration"></a>专用 DNS 区域集成的限制

当你删除专用终结点或从 Azure Cosmos 帐户中删除某个区域时，专用 DNS 区域中的 DNS 记录不会自动删除。 必须先手动删除 DNS 记录，然后才能执行以下操作：

* 添加链接到此专用 DNS 区域的新专用终结点。
* 将新区域添加到具有链接到此专用 DNS 区域的专用终结点的任何数据库帐户。

如果不清理 DNS 记录，则可能会出现意外的数据平面问题。 这些问题包括在删除私有终结点或删除区域后添加到区域的数据中断。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Cosmos DB 安全功能，请参阅以下文章：

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](firewall-support.md)。

* 若要了解如何为 Azure Cosmos 帐户配置虚拟网络服务终结点，请参阅[配置来自虚拟网络的访问权限](how-to-configure-vnet-service-endpoint.md)。

* 若要了解有关专用链接的详细信息，请参阅[Azure 专用链接](../private-link/private-link-overview.md)文档。
