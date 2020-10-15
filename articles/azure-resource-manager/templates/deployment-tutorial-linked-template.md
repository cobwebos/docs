---
title: 教程 - 部署链接模板
description: 了解如何部署链接模板
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: a90bb90c90206ffe00f8b4f2d035c0ea844b5c47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611667"
---
# <a name="tutorial-deploy-a-linked-template"></a>教程：部署链接模板

[前一篇教程](./deployment-tutorial-local-template.md)已介绍如何部署存储在本地计算机中的模板。 若要部署复杂的解决方案，可将一个模板分解为多个模板，并通过主模板部署这些模板。 本教程介绍如何部署包含对链接模板的引用的主模板。 部署主模板时，会触发链接模板的部署。 本教程还将介绍如何使用 SAS 令牌来存储和保护链接模板。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成前一篇教程，但这不是一项要求。

## <a name="review-template"></a>审阅模板

在前面的教程中，你部署了一个用于创建存储帐户、应用服务计划和 Web 应用的模板。 使用的模板是：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>创建链接模板

可将存储帐户资源隔离到链接模板中：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

以下模板是主模板。  突出显示的 **Microsoft.Resources/deployments** 对象演示如何调用链接模板。 无法将链接模板存储为本地文件，或存储为只能在本地网络中使用的文件。 只能提供包含 *http* 或 *https* 的 URI 值。 资源管理器必须能够访问该模板。 一种做法是将链接模板放入存储帐户，并对该项使用 URI。 该 URI 将通过参数传递给模板。 请参阅突出显示的参数定义。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

将主模板的副本保存到扩展名为 .json 的本地计算机，例如 azuredeploy.json。 无需保存链接模板的副本。  链接模板将从 GitHub 存储库复制到存储帐户。

## <a name="store-the-linked-template"></a>存储链接模板

以下 PowerShell 脚本将创建存储帐户，创建容器，然后将链接模板从 GitHub 存储库复制到该容器。 链接模板的副本存储在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json) 中。

选择“尝试”打开 Cloud Shell，选择“复制”以复制 PowerShell 脚本，然后右键单击 Shell 窗格以粘贴该脚本：  

> [!IMPORTANT]
> 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。 该名称必须是唯一的。 在模板中，存储帐户名称是追加了“store”的项目名称，项目名称的长度必须介于 3 到 11 个字符之间。 因此，项目名称必须符合存储帐户名称要求，且短于 11 个字符。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>部署模板

要在存储帐户中部署专用模板，请生成 SAS 令牌，并将其包括在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。 只有帐户所有者才能访问包含该模板的 Blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好办法，但不应直接在模板中包括密码等敏感数据。

如果尚未创建资源组，请参阅[创建资源组](./deployment-tutorial-local-template.md#create-resource-group)。

> [!NOTE]
> 在下面的 Azure CLI 代码中，日期参数 -d 在 macOS 中将会是无效参数。 因此，macOS 用户若要在 macOS 的终端中将当前时间增加 2 小时，则应使用 -v+2H。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理你部署的资源。

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。 
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

## <a name="next-steps"></a>后续步骤

现在，你已了解如何部署链接模板。 下一篇教程将介绍如何创建用于部署模板的 DevOp 管道。

> [!div class="nextstepaction"]
> [创建管道](./deployment-tutorial-pipeline.md)
