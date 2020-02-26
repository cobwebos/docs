---
title: 创建高级 Azure 文件共享
description: 本文介绍如何创建高级 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 237846ec3adda208126aeb22e7900cbf5118ee95
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598657"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>如何创建高级 Azure 文件共享
高级文件共享在固态磁盘（SSD）存储媒体上提供，适用于 IO 密集型工作负荷，包括宿主数据库和高性能计算（HPC）。 高级文件共享以特殊用途存储帐户类型（称为 FileStorage 帐户）托管。 高级文件共享旨在实现高性能和企业级应用程序，提供一致的低延迟、高 IOPS 和高吞吐量共享。

本文介绍如何使用[Azure 门户](https://portal.azure.com/)、Azure PowerShell 和 Azure CLI 创建此新帐户类型。

## <a name="prerequisites"></a>必备条件

若要访问包含高级 Azure 文件共享的 Azure 资源，你将需要一个 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>使用 Azure 门户创建高级文件共享

### <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="create-a-filestorage-storage-account"></a>创建 filestorage 存储帐户

现在，你已准备好创建你的存储帐户。

每个存储帐户都必须属于 Azure 资源组。 资源组是对 Azure 资源进行分组的逻辑容器。 在创建存储帐户时，可以选择创建新的资源组，也可以使用现有资源组。 本文介绍如何创建新的资源组。

1. 在 Azure 门户的左侧菜单中，选择 "**存储帐户**"。

    ![Azure 门户主页选择存储帐户](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 在显示的“存储帐户”窗口中，选择“添加”。
1. 选择要在其中创建存储帐户的订阅。
1. 在“资源组”字段下，选择“新建”。 输入新资源组的名称，如下图中所示。

1. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
1. 选择存储帐户的位置或使用默认位置。
1. 对于**性能**，请选择**高级**。
1. 选择 "**帐户类型**"，然后选择 " **FileStorage**"。
1. 将**复制**设置为其默认值 "**本地冗余存储（LRS）** "。

    ![如何创建高级文件共享的存储帐户](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. 选择“查看+创建”可查看存储帐户设置并创建帐户。
1. 选择“创建”。

创建存储帐户资源后，请导航到该资源。

### <a name="create-a-premium-file-share"></a>创建高级文件共享

1. 在存储帐户的左侧菜单中，滚动到 "**文件服务**" 部分，然后选择 "**文件**"。
1. 选择 "**文件共享**" 以创建高级文件共享。
1. 输入文件共享的名称和所需配额，然后选择 "**创建**"。

> [!NOTE]
> 预配的共享大小由共享配额指定，文件共享按预配大小计费，有关更多详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

   ![创建高级文件共享](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>清理资源

如果要清理本文中创建的资源，只需删除资源组即可。 删除资源组也会删除相关联的存储帐户，以及与资源组相关联的任何其他资源。

## <a name="create-a-premium-file-share-using-powershell"></a>使用 PowerShell 创建高级文件共享

### <a name="create-an-account-using-powershell"></a>使用 PowerShell 创建帐户

首先，安装最新版本的 [PowerShellGet](/powershell/scripting/gallery/installing-psget) 模块。

然后，升级 PowerShell 模块，登录到你的 Azure 订阅，创建一个资源组，然后创建一个存储帐户。

### <a name="upgrade-your-powershell-module"></a>升级 PowerShell 模块

若要从 PowerShell 与高级文件共享交互，需要安装 Az. Storage module 版本1.4.0 或最新的 Az 模块。

首先使用提升的权限打开 PowerShell 会话。

安装 Az 模块：

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

### <a name="create-a-filestorage-storage-account"></a>创建 FileStorage 存储帐户

若要从 PowerShell 创建 FileStorage 存储帐户，请使用[AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)命令：

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>创建高级文件共享

现在，你已有了 FileStorage 帐户，你可以创建高级文件共享。 使用[AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet 创建一个。

> [!NOTE]
> 预配的共享大小由共享配额指定，文件共享按预配大小计费，有关更多详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>使用 Azure CLI 创建高级文件共享

若要启动 Azure Cloud Shell，请登录到 [Azure 门户](https://portal.azure.com)。

如果要登录到 CLI 的本地安装，请首先确保安装了最新版本，然后运行 login 命令：

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

### <a name="create-a-filestorage-storage-account"></a>创建 FileStorage 存储帐户

若要从 Azure CLI 创建 FileStorage 存储帐户，请使用[az storage account create](/cli/azure/storage/account)命令。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>获取存储帐户密钥

存储帐户密钥控制对存储帐户中资源的访问，在本文中，我们将使用密钥来创建高级文件共享。 这些密钥是在创建存储帐户时自动创建的。 可以使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令获取存储帐户的存储帐户密钥：

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>创建高级文件共享

现在，你已有了 filestorage 帐户，你可以创建高级文件共享。 使用[az storage share create](/cli/azure/storage/share)命令创建一个。

> [!NOTE]
> 预配的共享大小由共享配额指定，文件共享按预配大小计费，有关更多详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

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

本文介绍了如何创建高级文件共享。 若要了解此帐户所提供的性能，请继续阅读规划指南中的 "性能层" 部分。

> [!div class="nextstepaction"]
> [文件共享层](storage-files-planning.md#storage-tiers)