---
title: 使用模板导入 SQL BACPAC 文件
description: 了解如何使用 Azure SQL 数据库扩展，以通过 Azure 资源管理器模板导入 SQL BACPAC 文件。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 500e07296040305f1e469fde78988f2551440e58
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471192"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>教程：使用 Azure 资源管理器模板导入 SQL BACPAC 文件

了解如何使用 Azure SQL 数据库扩展，以通过 Azure 资源管理器模板导入 BACPAC 文件。 部署项目包括主模板文件以及完成部署所需的任何文件。 BACPAC 文件是一个项目。 

在本教程中，你将创建一个模板来部署 Azure SQL Server、SQL 数据库并导入 BACPAC 文件。 要了解如何使用 Azure 资源管理器模板来部署 Azure 虚拟机扩展，请参阅[教程：使用 Azure 资源管理器模板部署虚拟机扩展](./template-tutorial-deploy-vm-extensions.md)。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 准备 BACPAC 文件。
> * 打开快速入门模板。
> * 编辑模板。
> * 部署模板。
> * 验证部署。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](./use-vs-code-to-create-template.md)。
* 要增强安全性，请使用为 Azure SQL Server 管理员帐户生成的密码。 以下是可用于生成密码的示例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault 旨在保护加密密钥和其他机密。 有关详细信息，请参阅[教程：在资源管理器模板部署中集成 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我们还建议你每三个月更新一次密码。

## <a name="prepare-a-bacpac-file"></a>准备 BACPAC 文件

BACPAC 文件在 [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) 中共享。 若要创建自己的文件，请参阅[将 Azure SQL 数据库导出到 BACPAC 文件](../../sql-database/sql-database-export.md)。 如果选择将文件发布到你自己的位置，则必须在教程的后面部分更新模板。

必须先将 BACPAC 文件存储在 Azure 存储帐户中，然后才能使用资源管理器模板导入该文件。 下面的 PowerShell 脚本通过以下步骤准备 BACPAC 文件：

* 下载 BACPAC 文件。
* 创建 Azure 存储帐户。
* 创建存储帐户 blob 容器。
* 将 BACPAC 文件上传到该容器。
* 显示存储帐户密钥和 blob URL。

1. 选择“试用”以打开 Cloud Shell  。 然后，将以下 PowerShell 脚本粘贴到 Shell 窗口中。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 记下存储帐户密钥和 BACPAC 文件 URL。 部署模板时需要这些值。

## <a name="open-a-quickstart-template"></a>打开快速入门模板

本教程中使用的模板存储在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) 中。

1. 在 Visual Studio Code 中，选择“文件” > “打开文件”。  
1. 在“文件名”中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. 选择“打开”以打开该文件。 

    模板中定义了两个资源：

   * `Microsoft.Sql/servers` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers)。
   * `Microsoft.SQL.servers/databases` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)。

        在自定义模板之前，不妨对其进行一些基本的了解。
1. 选择“文件” > “另存为”，将该文件的副本保存到名为 *azuredeploy.json* 的本地计算机。  

## <a name="edit-the-template"></a>编辑模板

1. 在“参数”部分的末尾再添加两个参数，以设置存储帐户密钥和 BACPAC URL  。

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    在“adminPassword”后面添加一个逗号  。 要从 Visual Studio Code 设置 JSON 文件的格式，请选择 Shift+Alt+F。

    要获取这两个值，请参阅[准备 BACPAC 文件](#prepare-a-bacpac-file)。

1. 向模板添加两个其他资源。

    * 要允许 SQL 数据库扩展导入 BACPAC 文件，需允许来自 Azure 服务的流量通过。 在 SQL Server 定义下添加以下防火墙规则定义：

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        该模板如下所示：

        ![包含防火墙规则定义的模板](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * 使用以下 JSON 将 SQL 数据库扩展资源添加到数据库定义：

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        该模板如下所示：

        ![包含 SQL 数据库扩展的模板](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        若要了解资源定义，请参阅 [SQL 数据库扩展参考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions)。 下面是一些重要元素：

        * **dependsOn**：必须在创建 SQL 数据库以后才能创建扩展资源。
        * **storageKeyType**：指定要使用的存储密钥的类型。 值可以是 `StorageAccessKey` 或 `SharedAccessKey`。 在本教程中使用 `StorageAccessKey`。
        * **storageKey**：指定存储 BACPAC 文件的存储帐户的密钥。 如果存储密钥类型为 `SharedAccessKey`，则必须以“?”为前缀。
        * **storageUri**：指定存储帐户中存储的 BACPAC 文件的 URL。
        * **administratorLoginPassword**：SQL 管理员的密码。 使用生成的密码。 请参阅[先决条件](#prerequisites)。

已完成的模板如下所示：

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>部署模板

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

有关部署过程，请参阅[部署模板](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)部分。 改用以下 PowerShell 部署脚本：

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

请考虑使用准备 BACPAC 文件时所用的项目名称，以便将所有资源存储在同一资源组内。 这样，就可以更轻松地管理资源任务，例如清理资源。 如果使用相同的项目名称，则可以从脚本中删除 `New-AzResourceGroup` 命令，或者在系统询问是否要更新现有资源组时回答“是 (y)”或“否 (n)”。

使用生成的密码。 请参阅[先决条件](#prerequisites)。

## <a name="verify-the-deployment"></a>验证部署

若要从客户端计算机访问 SQL Server，需要添加其他防火墙规则。 有关详细信息，请参阅[创建和管理 IP 防火墙规则](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)。

在 Azure 门户中，从新部署的资源组中选择 SQL 数据库。 选择“查询编辑器(预览)”，然后输入管理员凭据。  此时会看到两个表导入到数据库中。

![查询编辑器（预览）](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中，选择“资源组”  。
1. 在“按名称筛选”字段中输入资源组名称。 
1. 选择资源组名称。 应该会看到，该资源组中总共有六个资源。
1. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你部署了 SQL Server、SQL 数据库并导入了 BACPAC 文件。 BACPAC 文件存储在 Azure 存储帐户中。 得到该 URL 的任何人都可以访问该文件。 要了解如何保护 BACPAC 文件（项目），请参阅：

> [!div class="nextstepaction"]
> [保护项目](./template-tutorial-secure-artifacts.md)
