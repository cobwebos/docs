---
title: 使用资源管理器模板创建和管理 Azure Cosmos DB
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB for SQL (Core) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390886"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB SQL (Core) API 资源

在本文中，你将了解如何使用 Azure 资源管理器模板来帮助自动管理 Azure Cosmos DB 帐户、数据库和容器。

本文仅展示了适用于 SQL API 帐户的 Azure 资源管理器模板示例。 你还可以查找适用于 [Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md) 和[表](manage-table-with-resource-manager.md) API 的模板示例。

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>创建 Azure Cosmos 帐户、数据库和容器

下面的 Azure 资源管理器模板创建包含以下项的 Azure Cosmos 帐户：

* 两个在数据库级别共享 400 请求单位/秒 (RU/s) 吞吐量的容器。
* 一个具有专用 400 RU/s 吞吐量的容器。

若要创建 Azure Cosmos DB 资源，请复制下面的示例模板，并按照说明通过 [PowerShell](#deploy-via-powershell) 或 [Azure CLI](#deploy-via-azure-cli) 进行部署。

* 还可以访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)，然后从 Azure 门户部署模板。
* 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

> [!IMPORTANT]
>
> * 在 Azure Cosmos 帐户中添加或删除位置时，不能同时修改其他属性。 这些操作必须单独执行。
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请使用更新后的 RU/s 重新提交模板。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> 若要使用大的分区键创建容器，请修改前面的模板以在 `partitionKey` 对象内包括 `"version":2` 属性。

### <a name="deploy-via-powershell"></a>通过 PowerShell 部署

若要使用 PowerShell 部署 Azure 资源管理器模板，请执行以下操作：

1. **复制**脚本。
2. 选择 **"尝试"** 以打开 Azure 云外壳。
3. 右键单击 Azure 云壳窗口，然后选择 **"粘贴**"。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

您可以选择使用本地安装的 PowerShell 版本而不是 Azure 云外壳部署模板。 你需要[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 请运行 `Get-Module -ListAvailable Az` 来查找所需的版本。

### <a name="deploy-via-azure-cli"></a>通过 Azure CLI 部署

若要使用 Azure CLI 部署 Azure 资源管理器模板，请执行以下操作：

1. **复制**脚本。
2. 选择 **"尝试"** 以打开 Azure 云外壳。
3. 右键单击 Azure 云壳窗口，然后选择 **"粘贴**"。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 命令显示预配后的新建 Azure Cosmos 帐户。 您可以选择使用本地安装的 Azure CLI 版本而不是 Azure 云外壳部署模板。 有关详细信息，请参阅 [Azure 命令行接口 (CLI)](/cli/azure/) 一文。

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>使用服务器端功能创建 Azure Cosmos DB 容器

可以使用 Azure 资源管理器模板创建包含存储过程、触发器和用户定义的函数的 Azure Cosmos DB 容器。

复制下面的示例模板，并按照说明通过 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 进行部署。

* 还可以访问 [Azure 快速入门库](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/)，然后从 Azure 门户部署模板。
* 还可以将模板下载到本地计算机，或者创建新模板并使用 `--template-file` 参数指定本地路径。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>使用 PowerShell 部署

若要使用 PowerShell 部署 Azure 资源管理器模板，请执行以下操作：

1. **复制**脚本。
1. 选择 **"尝试"** 以打开 Azure 云外壳。
1. 右键单击 Azure 云壳体窗口，然后选择 **"粘贴**"。

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

您可以选择使用本地安装的 PowerShell 版本而不是 Azure 云外壳部署模板。 你需要[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 请运行 `Get-Module -ListAvailable Az` 来查找所需的版本。

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要使用 Azure CLI 部署 Azure 资源管理器模板，请执行以下操作：

1. **复制**脚本。
2. 选择 **"尝试"** 以打开 Azure 云外壳。
3. 右键单击 Azure 云壳窗口，然后选择 **"粘贴**"。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Azure 资源管理器文档](/azure/azure-resource-manager/)
* [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [排查常见的 Azure 资源管理器部署错误](../azure-resource-manager/templates/common-deployment-errors.md)
