---
title: 为 Azure Cosmos 帐户配置 Azure 专用链接
description: 了解如何设置 Azure 专用链接，以便在虚拟网络中使用专用 IP 地址访问 Azure Cosmos 帐户。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 1eb769ec64e50be65d63be43d897c1190789e555
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518757"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>为 Azure Cosmos 帐户配置 Azure 专用链接（预览）

使用 Azure 专用链接，可以通过专用终结点连接到 Azure Cosmos 帐户。 专用终结点是虚拟网络内子网中的一组专用 IP 地址。 通过使用 "专用链接"，可以通过专用 IP 地址限制对给定 Azure Cosmos 帐户的访问。 结合受限制的 NSG 策略时，专用链接有助于降低数据渗透的风险。 若要了解有关专用终结点的详细信息，请参阅[Azure Private Link](../private-link/private-link-overview.md)一文。

此外，专用链接允许从虚拟网络或任何对等互连虚拟网络中访问 Azure Cosmos 帐户。 映射到专用链接的资源也可通过 VPN 或 ExpressRoute 通过专用对等互连从本地访问。 

你可以使用 "自动" 或 "手动" 审批方法连接到使用 "专用" 链接配置的 Azure Cosmos 帐户。 若要了解详细信息，请参阅专用链接文档的 "[批准工作流](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)" 部分。 本文介绍了创建专用链接的步骤，假定您使用的是自动批准方法。

## <a name="create-a-private-link-using-the-azure-portal"></a>使用 Azure 门户创建专用链接

使用以下步骤，通过 Azure 门户为现有的 Azure Cosmos 帐户创建专用链接：

1. 在 "**所有资源**" 窗格中，找到要保护的 Azure Cosmos DB 帐户。

1. 从 "设置" 菜单选择 "**专用终结点连接**"，然后选择 "**专用终结点**" 选项：

   ![使用 Azure 门户创建专用终结点](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. 在 "**创建专用终结点（预览）-基本**信息" 窗格中，输入或选择以下详细信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选一个择资源组。|
    | **实例详细信息** |  |
    | 名称 | 输入专用终结点的任意名称;如果使用此名称，请创建一个唯一的名称。 |
    |区域| 选择要在其中部署专用链接的区域。 应在虚拟网络所在的同一位置创建专用终结点。|
    |||
1. 选择 "**下一步：资源**"。
1. 在 "**创建专用终结点-资源**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择 "连接到我的目录中的 Azure 资源"。 <br/><br/> 使用此选项，可以选择一个资源来设置专用链接，或使用与你共享的资源 ID 或别名连接到其他人的资源。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择 " **AzureCosmosDB/databaseAccounts**"。 |
    | 资源 |选择你的 Azure Cosmos 帐户 |
    |目标子资源 |选择要映射的 Cosmos DB API 类型。 默认情况下，对于 SQL、MongoDB 和 Cassandra Api，这是唯一的选择。 对于 Gremlin 和表 Api，还可以选择 " *sql* "，因为这些 api 可与 Sql API 进行互操作。 |
    |||
1. 选择**下一步：配置**。
1. 在 "**创建专用终结点（预览版）-配置**" 中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**联网**| |
    | 虚拟网络| 选择虚拟网络。 |
    | 子网 | 选择子网。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择*privatelink.documents.azure.com* |
    |||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。
1. 看到“验证通过”消息时，选择“创建”。

如果已批准 Azure Cosmos 帐户的专用链接，请在 "**防火墙和虚拟网络**" 窗格中 Azure 门户 "**所有网络**" 选项。

### <a name="fetch-the-private-ip-addresses"></a>提取专用 IP 地址

预配专用终结点后，可以查询 IP 地址。 查看 Azure 门户中的 IP 地址。 选择 "**所有资源**"，搜索之前创建的专用终结点（在此示例中为 "dbPrivateEndpoint3"），并选择 "概述" 选项卡以查看 DNS 设置和 IP 地址：

![Azure 门户中的专用 IP 地址](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

每个专用终结点创建多个 IP 地址：

* 一个用于 Azure Cosmos 帐户的全局（区域不可知）终结点。
* 其中一个是部署 Azure Cosmos 帐户的每个区域。

## <a name="create-a-private-link-using-azure-powershell"></a>使用 Azure PowerShell 创建专用链接

运行以下 PowerSehll 脚本，为现有的 Azure Cosmos 帐户创建名为 "MyPrivateEndpoint" 的专用链接。 请确保将变量值替换为特定于您的环境的详细信息。

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

## <a name="create-a-private-link-using-a-resource-manager-template"></a>使用资源管理器模板创建专用链接

可以通过在虚拟网络子网中创建专用终结点来设置专用链接。 这是通过使用 Azure 资源管理器模板来实现的。 使用以下代码创建一个名为 "PrivateEndpoint_template" 的资源管理器模板。 此模板在现有虚拟网络中创建现有 Azure Cosmos SQL API 帐户的专用终结点：

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

### <a name="define-the-template-parameters-file"></a>定义模板参数文件

为该模板创建一个参数文件，并将其命名为 "PrivateEndpoint_parameters"。 将以下代码添加到参数文件：

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>使用 PowerShell 脚本部署模板

接下来，使用以下代码创建 PowerShell 脚本。 在运行该脚本之前，请确保将订阅 ID、资源组名称和其他变量值替换为特定于您的环境的详细信息：

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

在 PowerShell 脚本中，"GroupId" 变量只能包含一个值，该值是帐户的 API 类型。 允许的值为： SQL、MongoDB、Cassandra、Gremlin 和 Table。 某些 Azure Cosmos 帐户类型可通过多个 Api 访问。 例如：

* 可以从 Gremlin 和 SQL API 帐户访问 Gremlin API 帐户。
* 可以从表和 SQL API 帐户访问表 API 帐户。

对于此类帐户，你必须为每个 API 类型创建一个专用终结点，并在 "GroupId" 数组中指定相应的 API 类型。

成功部署模板后，可以看到与下图中显示的内容类似的输出。 如果正确设置了专用终结点，则 provisioningState 值为 "Succeeded"。

![资源管理器模板部署输出](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

部署模板后，子网中会保留专用 IP 地址。 Azure Cosmos 帐户的防火墙规则配置为仅接受来自专用终结点的连接。

## <a name="configure-private-dns"></a>配置专用 DNS

在专用链接预览过程中，应在创建专用终结点的子网中使用专用 DNS。 和配置终结点，以便将每个专用 IP 地址映射到 DNS 条目（请参阅上面显示的响应中的 "fqdn" 属性）。

## <a name="firewall-configuration-with-private-link"></a>具有专用链接的防火墙配置

当你结合使用专用链接和防火墙规则时，以下是不同的情况和结果：

* 如果未配置防火墙规则，则默认情况下，所有流量都可以访问 Azure Cosmos 帐户。

* 如果配置了公共流量或服务终结点，并且创建了专用终结点，则会通过相应类型的防火墙规则授权不同类型的传入流量。

* 如果未配置公共流量或服务终结点并创建专用终结点，则只能通过专用终结点访问 Azure Cosmos 帐户。

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>添加或删除区域时更新专用终结点

若要在 Azure Cosmos 帐户中添加或删除区域，需要在该帐户中添加或删除 DNS 条目。 应在私有终结点中相应地更新这些更改。 目前，你应使用以下步骤手动完成此更改：

1. Azure Cosmos DB 管理员添加或删除区域。 然后，系统会通知网络管理员有关挂起的更改。 映射到 Azure Cosmos 帐户的专用终结点看到其 "ActionsRequired" 属性从 "无" 更改为 "重新创建"。 然后，网络管理员通过使用用于创建 PUT 请求的相同资源管理器负载发出 PUT 请求来更新专用终结点。

1. 完成此操作后，还必须更新子网的专用 DNS，以反映已添加或已删除的 DNS 条目及其相应的专用 IP 地址。

例如，如果在3个区域中部署 Azure Cosmos 帐户： "美国西部"、"美国中部" 和 "西欧"。 当你为帐户创建专用终结点时，子网中保留了4个专用 Ip。 每个区域一个，计算总计3，另一个用于全局/地区无关的终结点。

稍后，如果向 Azure Cosmos 帐户添加新的区域，例如 "美国东部"。 默认情况下，不能从现有的专用终结点访问新区域。 Azure Cosmos 帐户管理员应刷新专用终结点连接，然后才能访问它。

运行 ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` 命令时，该命令的输出将包含设置为 "重新创建" 的 `ActionRequired` 参数。 此值指示应刷新专用终结点。 接下来，Azure Cosmos 帐户管理员运行 `Set-AzPrivateEndpoint` 命令来触发专用终结点刷新。

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

新的专用 IP 会自动保留在此专用终结点下的子网中，并且值 `ActionRequired` 成为 `None`。 如果你没有任何专用 DNS 区域集成（换言之，如果你使用的是自定义专用 DNS），则必须配置专用 DNS，以便为与新区域相对应的专用 IP 添加新的 DNS 记录。

删除区域时，可以使用相同的步骤。 已删除区域的专用 IP 会自动回收，`ActionRequired` 标志将变为 `None`。 如果没有任何专用 DNS 区域集成，则必须将专用 DNS 配置为删除已删除区域的 DNS 记录。

## <a name="current-limitations"></a>当前限制

使用 Azure Cosmos 帐户的私有链接时，有以下限制：

* 使用直接模式连接的 Azure Cosmos 帐户使用专用链接时，只能使用 TCP 协议。 HTTP 协议尚不受支持

* 使用 Azure Cosmos DB 的 MongoDB 帐户的 API 时，仅支持服务器版本3.6 上的帐户（这是使用 `*.mongo.cosmos.azure.com`格式的终结点的帐户）。 服务器版本3.2 上的帐户不支持专用链接（这是使用 `*.documents.azure.com`格式的终结点的帐户）。 若要使用专用链接，你应该将旧帐户迁移到新版本。

* 使用 Azure Cosmos DB 的 API 进行具有私有链接的 MongoDB 帐户时，无法使用 Robo 3T、Studio 3T、Mongoose 等工具。仅当指定 appName =<account name> 参数时，终结点才能具有专用链接支持。 例如： replicaSet = globaldb & appName = mydbaccountname。 由于这些工具不会在连接字符串中将应用程序名称传递到服务，因此无法使用专用链接。 不过，你仍然可以使用3.6 版本的 SDK 驱动程序访问这些帐户。

* 仅在特定区域提供对 Azure Cosmos 帐户和 Vnet 的私有链接支持。 有关支持的区域的列表，请参阅私有链接文章的 "[可用区域](../private-link/private-link-overview.md#availability)" 部分。

* 如果虚拟网络包含 "专用" 链接，则无法将其移动或删除。

* 如果 Azure Cosmos 帐户附加到专用终结点，则无法将其删除。

* Azure Cosmos 帐户无法故障转移到未映射到附加到它的所有专用终结点的区域。 有关详细信息，请参阅上一部分中的添加或删除区域。

* 管理员可以在 Azure Cosmos 帐户范围内至少向网络管理员授予 "*/PrivateEndpointConnectionsApproval" 权限，以创建自动批准的专用终结点。

## <a name="next-steps"></a>后续步骤

若要了解有关其他 Azure Cosmos DB 安全功能的详细信息，请参阅以下文章：

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](firewall-support.md)。

* [如何为 Azure Cosmos 帐户配置虚拟网络服务终结点。](how-to-configure-vnet-service-endpoint.md)

* 若要了解有关专用链接的详细信息，请参阅[Azure 专用链接](../private-link/private-link-overview.md)文档。
