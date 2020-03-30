---
title: 创建块 Blob 存储帐户 - Azure 存储 |微软文档
description: 演示如何创建具有高级性能特征的 Azure BlockBlob 存储帐户。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536898"
---
# <a name="create-a-blockblobstorage-account"></a>创建块 Blob 存储帐户

BlockBlob 存储帐户类型允许您创建具有高级性能特征的块 Blob。 这种类型的存储帐户针对事务速率高或访问时间非常快的工作负载进行了优化。 本文演示如何使用 Azure 门户、Azure CLI 或 Azure PowerShell 创建块 Blob 存储帐户。

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

有关块 Blob 存储帐户的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[门户](#tab/azure-portal)

无。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

此"执行"文章需要 Azure PowerShell 模块 Az 版本 1.2.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以登录到 Azure，然后采用以下两种方式之一运行 Azure CLI 命令：

- 您可以在 Azure 云外壳中从 Azure 门户中运行 CLI 命令。
- 可以安装 CLI 并在本地运行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 Azure CLI 已预安装并配置为与您的帐户一起使用。 单击 Azure 门户右上角菜单中的 **"云壳"** 按钮：

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

该按钮将启动一个交互式外壳，可用于运行本操作操作文章中概述的步骤：

[![显示门户中的云壳窗口的屏幕截图](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本地安装 CLI

也可在本地安装和使用 Azure CLI。 此"执行"文章要求您运行 Azure CLI 版本 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

---

## <a name="sign-in-to-azure"></a>登录 Azure

# <a name="portal"></a>[门户](#tab/azure-portal)

登录到 Azure[门户](https://portal.azure.com)。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要启动 Azure 云外壳，请登录到[Azure 门户](https://portal.azure.com)。

若要登录到本地安装的 CLI，请运行 [az login](/cli/azure/reference-index#az-login) 命令：

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>创建块 Blob 存储帐户

## <a name="portal"></a>[门户](#tab/azure-portal)
要在 Azure 门户中创建块 Blob 存储帐户，请按照以下步骤操作：

1. 在 Azure 门户中，选择存储类别**Storage**>**存储帐户**>**所有服务**。

1. 在 **"存储帐户**"下，选择 **"添加**"。

1. 在 **"订阅"** 字段中，选择要在其中创建存储帐户的订阅。

1. 在 **"资源组"** 字段中，选择现有资源组或选择 **"创建新**"，然后输入新资源组的名称。

1. 在 **"存储帐户名称**"字段中，输入帐户的名称。 请注意以下准则：

   - 该名称在 Azure 中必须唯一。
   - 名称必须长在 3 到 24 个字符之间。
   - 名称只能包括数字和小写字母。

1. 在 **"位置"** 字段中，为存储帐户选择位置或使用默认位置。

1. 对于其余设置，请配置以下内容：

   |字段     |“值”  |
   |---------|---------|
   |**性能**    |  选择 **"高级**"。   |
   |**帐户种类**    | 选择**块 Blob 存储**。      |
   |**复制**    |  保留**本地冗余存储 （LRS） 的**默认设置。      |

   ![显示门户 UI 以创建块 Blob 存储帐户](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 选择 **"查看 + 创建**"以查看存储帐户设置。

1. 选择 **“创建”**。

## <a name="azure-powershell"></a>[Azure 电源外壳](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行）。

1. 运行以下命令以确保安装了`Az`最新版本的 PowerShell 模块。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 打开新的 PowerShell 控制台，然后使用 Azure 帐户登录。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 如果需要，请创建新的资源组。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 创建块 Blob 存储帐户。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 创建块 Blob 帐户，必须首先安装 Azure CLI v。 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 登录到 Azure 订阅。

   ```azurecli
   az login
   ```

1. 如果需要，请创建新的资源组。 替换括号中的值（包括括号），并运行以下命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. 创建块 Blob 存储帐户。 替换括号中的值（包括括号），并运行以下命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

---

## <a name="next-steps"></a>后续步骤

- 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
