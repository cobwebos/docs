---
title: Azure 资源管理器：创建单一数据库
description: 使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 5d090add7bdb2c3ee08f4c186bd57d63f14ab113
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422554"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](sql-database-single-database.md)是最快速且最简单的部署选项。 本快速入门介绍如何使用 Azure 资源管理器模板创建单一数据库。 有关详细信息，请参阅 [Azure 资源管理器文档](/azure/azure-resource-manager/)。

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-single-database"></a>创建单一数据库

单一数据库有一组通过两个[购买模型](sql-database-purchase-models.md)中的一个定义的计算、内存和存储资源。 创建单一数据库时，也定义一个 [SQL 数据库服务器](sql-database-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中。

以下 JSON 文件是本文中使用的模板。 该模板存储在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json) 中。 可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多 Azure SQL 数据库模板示例。

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

从以下 PowerShell 代码块中选择“试用”  以打开 Azure Cloud Shell。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'adminUser=' + $adminUser \
                 'adminPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="query-the-database"></a>查询数据库

若要查询数据库，请参阅[查询数据库](./sql-database-single-database-get-started.md#query-the-database)。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、数据库服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

若要删除资源组，请执行以下操作：

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>后续步骤

- 创建服务器级防火墙规则，以便通过本地或远程工具连接到单一数据库。 有关详细信息，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。
- 创建服务器级防火墙规则后，使用多种不同的工具和语言[连接和查询](sql-database-connect-query.md)数据库。
  - [使用 SQL Server Management Studio 连接和查询](sql-database-connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 若要使用 Azure CLI 创建单一数据库，请参阅 [Azure CLI 示例](sql-database-cli-samples.md)。
- 若要使用 Azure PowerShell 创建单一数据库，请参阅 [Azure PowerShell 示例](sql-database-powershell-samples.md)。
