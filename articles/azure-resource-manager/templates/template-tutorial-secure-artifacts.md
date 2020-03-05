---
title: 保护模板中的项目
description: 了解如何保护 Azure 资源管理器模板中使用的项目。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250108"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>教程：保护 Azure 资源管理器模板部署中的项目

了解如何使用 Azure 存储帐户和共享访问签名 (SAS) 保护 Azure 资源管理器模板中使用的项目。 部署项目是指完成部署所需的任何文件以及主模板文件。 例如，在[教程：使用 Azure 资源管理器模板导入 SQL BACPAC 文件](./template-tutorial-deploy-sql-extensions-bacpac.md)，主模板创建 Azure SQL 数据库实例。 它还调用一个 BACPAC 文件来创建表和插入数据。 BACPAC 文件是存储在 Azure 存储帐户中的项目。 存储帐户密钥用于访问该项目。 

在本教程中，你将使用 SAS 来授予对自己 Azure 存储帐户中 BACPAC 文件的有限访问权限。 有关 SAS 的详细信息，请参阅[使用共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

若要了解如何保护链接的模板，请参阅[教程：创建链接的 Azure 资源管理器模板](./template-tutorial-create-linked-templates.md)。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 准备 BACPAC 文件。
> * 打开现有模板。
> * 编辑模板。
> * 部署模板。
> * 验证部署。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

* 包含资源管理器工具扩展的 Visual Studio Code。 请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](./use-vs-code-to-create-template.md)。
* 查看[教程：使用 Azure 资源管理器模板导入 SQL BACPAC 文件](./template-tutorial-deploy-sql-extensions-bacpac.md)。 本教程中使用的模板是在该教程中开发的。 本文提供了已完成模板的下载链接。
* 若要增强安全性，请使用为 SQL Server 管理员帐户生成的密码。 以下是可用于生成密码的示例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 旨在保护加密密钥和其他机密。 有关详细信息，请参阅[教程：在资源管理器模板部署中集成 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我们还建议你每三个月更新一次密码。

## <a name="prepare-a-bacpac-file"></a>准备 BACPAC 文件

在本部分准备 BACPAC 文件，以便在部署资源管理器模板时可以安全访问该文件。 本部分包括五个过程：

* 下载 BACPAC 文件。
* 创建 Azure 存储帐户。
* 创建存储帐户 blob 容器。
* 将 BACPAC 文件上传到该容器。
* 检索 BACPAC 文件的 SAS 令牌。

1. 选择“试用”以打开 Cloud Shell  。 然后，将以下 PowerShell 脚本粘贴到 Shell 窗口中。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. 请记下 BACPAC 文件 URL 和 SAS 令牌。 部署模板时需要这些值。

## <a name="open-an-existing-template"></a>打开现有模板

在此会话中，修改在[教程：使用 Azure 资源管理器模板导入 SQL BACPAC 文件](./template-tutorial-deploy-sql-extensions-bacpac.md)中创建的模板，以通过 SAS 令牌调用 BACPAC 文件。 SQL 扩展教程中开发的模板将在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) 中共享。

1. 在 Visual Studio Code 中，选择“文件” > “打开文件”。  
1. 在“文件名”中粘贴以下 URL： 

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. 选择“打开”以打开该文件。 

    该模板中定义了四个资源：

   * `Microsoft.Sql/servers` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)。
   * `Microsoft.SQL/servers/firewallRules` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)。
   * `Microsoft.SQL/servers/databases` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)。
   * `Microsoft.SQL/server/databases/extensions` 列中的一个值匹配。 请参阅[模板参考](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)。

        在自定义模板之前，不妨对其进行一些基本的了解。
1. 选择“文件” > “另存为”，将该文件的副本保存到名为 *azuredeploy.json* 的本地计算机。  

## <a name="edit-the-template"></a>编辑模板

1. 将 storageAccountKey 参数定义替换为以下参数定义： 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![资源管理器教程安全项目参数](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. 更新 SQL 扩展资源的以下三个元素值：

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

已完成的模板如下所示：

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>部署模板

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

有关部署过程，请参阅[部署模板](./template-tutorial-create-multiple-instances.md#deploy-the-template)部分。 改用以下 PowerShell 部署脚本。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

使用生成的密码。 请参阅[先决条件](#prerequisites)。
有关 _artifactsLocation、_artifactsLocationSasToken 和 bacpacFileName 的值，请参阅[准备 BACPAC 文件](#prepare-a-bacpac-file)。

## <a name="verify-the-deployment"></a>验证部署

在门户中，从新部署的资源组中选择 SQL 数据库。 选择“查询编辑器(预览)”，然后输入管理员凭据。  此时会看到两个表导入到数据库中。

![查询编辑器（预览）](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。

1. 在 Azure 门户上的左侧菜单中，选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。 应该会看到，该资源组中总共有六个资源。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已部署 SQL Server、SQL 数据库，并已使用 SAS 令牌导入 BACPAC 文件。 若要了解如何创建 Azure Pipeline 以持续开发和部署资源管理器模板，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure Pipeline 进行持续集成](./template-tutorial-use-azure-pipelines.md)
