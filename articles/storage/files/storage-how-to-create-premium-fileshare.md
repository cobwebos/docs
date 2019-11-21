---
title: Create a premium Azure file share
description: In this article, you learn how to create a premium Azure file share.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4b3e51b5e8d0ee9b62a7e7bc39955396f327c7e7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209540"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>How to create an premium Azure file share
Premium file shares are offered on solid-state disk (SSD) storage media and are useful for IO-intensive workloads, including hosting databases and high-performance computing (HPC). Premium file shares are hosted in a special purpose storage account kind, called a FileStorage account. Premium file shares are designed for high performance and enterprise scale applications, providing consistent low latency, high IOPS, and high throughput shares.

This article shows you how to create this new account type using [Azure portal](https://portal.azure.com/), Azure PowerShell, and Azure CLI.

## <a name="prerequisites"></a>必备组件

To access Azure resources including premium Azure file shares, you'll need an Azure subscription. 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Create a premium file share using the Azure portal

### <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com/)。

### <a name="create-a-filestorage-storage-account"></a>Create a filestorage storage account

Now you're ready to create your storage account.

每个存储帐户都必须属于 Azure 资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。 This article shows how to create a new resource group.

1. In the Azure portal, select **Storage Accounts** on the left menu.

    ![Azure portal main page select storage account](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在显示的“存储帐户”窗口中，选择“添加”。
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称，如下图中所示。

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置或使用默认位置。
1. For **Performance** select **Premium**.
1. Select **Account kind** and choose **FileStorage**.
1. Leave **Replication** set to its default value of **Locally-redundant storage (LRS)** .

    ![How to create a storage account for a premium file share](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. 选择“查看+创建”可查看存储帐户设置并创建帐户。
1. 选择**创建**。

Once your storage account resource has been created, navigate to it.

### <a name="create-a-premium-file-share"></a>创建高级文件共享

1. In the left menu for the storage account, scroll to the **File service** section, then select **Files**.
1. Select **File share** to create a premium file share.
1. Enter a name and a desired quota for your file share, then select **Create**.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

   ![创建高级文件共享](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>清理资源

If you would like to clean up the resources created in this article, you can simply delete the resource group. Deleting the resource group also deletes the associated storage account as well as any other resources associated with the resource group.

## <a name="create-a-premium-file-share-using-powershell"></a>Create a premium file share using PowerShell

### <a name="create-an-account-using-powershell"></a>使用 PowerShell 创建帐户

首先，安装最新版本的 [PowerShellGet](/powershell/scripting/gallery/installing-psget) 模块。

然后，升级 powershell 模块，登录到 Azure 订阅，创建资源组，然后创建存储帐户。

### <a name="upgrade-your-powershell-module"></a>Upgrade your PowerShell module

To interact with a premium file share from with PowerShell, you'll need to install an Az.Storage module version 1.4.0, or the latest Az.Storage module.

首先使用提升的权限打开 PowerShell 会话。

Install the Az.Storage module:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

使用 `Connect-AzAccount` 命令并按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>创建资源组

若要通过 PowerShell 创建新的资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令：

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Create a FileStorage storage account

To create a filestorage storage account from PowerShell, use the [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) command:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>创建高级文件共享

Now that you have a FileStorage account, you can create a premium file share. Use the [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet to create one.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>清理资源

若要删除资源组及其关联的资源（包括新的存储帐户），请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令： 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Create a premium file share using Azure CLI

若要启动 Azure Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。

If you want to log into your local installation of the CLI, first make sure you have the latest version, then run the login command:

```cli
az login
```

### <a name="create-a-resource-group"></a>创建资源组

若要通过 Azure CLI 创建新的资源组，请使用 [az group create](/cli/azure/group) 命令。

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Create a FileStorage storage account

To create a FileStorage storage account from the Azure CLI, use the [az storage account create](/cli/azure/storage/account) command.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>获取存储帐户密钥

Storage account keys control access to resources in a storage account, in this article, we use the key in order to create a premium file share. 这些密钥是在创建存储帐户时自动创建的。 可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令获取存储帐户的存储帐户密钥：

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>创建高级文件共享

Now that you have a filestorage account, you can create a premium file share. Use the [az storage share create](/cli/azure/storage/share) command to create one.

> [!NOTE]
> Provisioned share sizes is specified by the share quota, file shares are billed on the provisioned size, refer to the [pricing page](https://azure.microsoft.com/pricing/details/storage/files/) for more details.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>清理资源

若要删除资源组及其关联的资源（包括新的存储帐户），请使用 [az group delete](/cli/azure/group) 命令。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

In this article, you've created a premium file share. To learn about the performance this account offers, continue to the performance tier section of the planning guide.

> [!div class="nextstepaction"]
> [File share performance tiers](storage-files-planning.md#file-share-performance-tiers)