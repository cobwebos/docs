---
title: Azure Cosmos DB 的资源管理器模板表 API
description: 使用 Azure 资源管理器模板来创建和配置 Azure Cosmos DB 表 API。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388068"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB 表 API 资源

本文介绍如何使用 Azure 资源管理器模板执行不同的操作来自动管理 Azure Cosmos DB 帐户、数据库和容器。 本文仅提供表 API 帐户的示例，若要查找其他 API 类型帐户的示例，请参阅：将 Azure 资源管理器模板用于[Cassandra](manage-cassandra-with-resource-manager.md)、 [Gremlin](manage-gremlin-with-resource-manager.md)、 [MongoDB](manage-mongodb-with-resource-manager.md)、 [SQL](manage-sql-with-resource-manager.md)文章的 Azure Cosmos DB API。

## 创建 Azure Cosmos 帐户和表<a id="create-resource"></a>

使用 Azure 资源管理器模板创建 Azure Cosmos DB 资源。 此模板将创建一个 Cosmos 的 Azure 表 API 帐户，其中一个表的吞吐量为 400 RU/秒。 复制模板并按如下所示进行部署，或访问[Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/)并从 Azure 门户部署。 还可以将模板下载到本地计算机，或者创建新模板，并使用 `--template-file` 参数指定本地路径。

> [!NOTE]
> 帐户名称必须是小写、44或更少字符。
> 若要更新 RU/秒，请用更新的吞吐量属性值重新提交模板。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>通过 PowerShell 进行部署

若要使用 PowerShell 部署资源管理器模板，请**复制**该脚本，然后选择 "**尝试**" 以打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择 "**粘贴**"：

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

如果选择使用本地安装的 PowerShell 版本，而不是 Azure Cloud shell，则必须[安装](/powershell/azure/install-az-ps)Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。

### <a name="deploy-via-the-azure-cli"></a>通过 Azure CLI 部署

若要使用 Azure CLI 部署 Azure 资源管理器模板，请**复制**该脚本，然后选择 "**尝试**" 以打开 Azure Cloud Shell。 若要粘贴脚本，请右键单击 shell，然后选择 "**粘贴**"：

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

"`az cosmosdb show`" 命令显示预配新创建的 Azure Cosmos 帐户。 如果选择使用 Azure CLI 本地安装的版本，而不是使用 Cloud Shell，请参阅[Azure CLI](/cli/azure/)文章。

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

- [Azure 资源管理器文档](/azure/azure-resource-manager/)
- [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)