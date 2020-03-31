---
title: Azure 资源管理器：创建单一数据库
description: 使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 7c42ff7f42dea049752f9f879abffffd0e7b3902
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531322"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](sql-database-single-database.md)是最快速且最简单的部署选项。 本快速入门介绍如何使用 Azure 资源管理器模板创建单一数据库。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

无

## <a name="create-a-single-database"></a>创建单一数据库

单一数据库有一组通过两个[购买模型](sql-database-purchase-models.md)中的一个定义的计算、内存和存储资源。 创建单一数据库时，也定义一个 [SQL 数据库服务器](sql-database-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../azure-resource-manager/management/overview.md)中。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-sql-logical-server/)。

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

该模板中定义了以下资源：

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servers/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/servers/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多 Azure SQL 数据库模板示例。

### <a name="deploy-the-template"></a>部署模板

从以下 PowerShell 代码块中选择“试用”  以打开 Azure Cloud Shell。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>验证部署

若要查询数据库，请参阅[查询数据库](./sql-database-single-database-get-started.md#query-the-database)。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、数据库服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

若要删除资源组，请执行以下操作：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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
- 若要了解如何创建资源管理器模板，请参阅[创建第一个模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)。
