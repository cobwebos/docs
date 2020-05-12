---
title: 创建块 Blob 存储帐户 - Azure 存储 | Microsoft Docs
description: 演示如何创建具有高级性能特征的 Azure BlockBlobStorage 帐户。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4767c0310783e7e2cc51b4caa7d6e6a052d0a05a
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007298"
---
# <a name="create-a-blockblobstorage-account"></a>创建 BlockBlobStorage 帐户

BlockBlobStorage 帐户类型允许创建具有高级性能特征的块 Blob。 此类存储帐户针对事务处理速率高或需要极快速访问的工作负载进行了优化。 本文介绍如何使用 Azure 门户、Azure CLI 或 Azure PowerShell 创建 BlockBlobStorage 帐户。

> [!NOTE]
> 块 blob 存储帐户中的分层命名空间功能以公共预览版提供，可在美国东部、美国东部2、美国中部、美国中南部、美国西部2、英国南部、加拿大中部和澳大利亚东部地区获得。 若要查看限制，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)和[已知问题](data-lake-storage-known-issues.md)。 若要注册预览版，请参阅[此窗体](https://aka.ms/adlspremiumonboard)。

有关 BlockBlobStorage 帐户的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

# <a name="portal"></a>[门户](#tab/azure-portal)

无。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

本操作指南文章需要 Azure PowerShell 模块 Az version 1.2.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以登录到 Azure，然后采用以下两种方式之一运行 Azure CLI 命令：

- 可以在 Azure Cloud Shell 中的 Azure 门户内运行 CLI 命令。
- 可以安装 CLI 并在本地运行 CLI 命令。

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 Azure CLI 已预安装并配置为与帐户一起使用。 单击 Azure 门户右上部分菜单上的 " **Cloud Shell** " 按钮：

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

此按钮会启动交互式 shell，你可以使用它来运行本操作指南文章中所述的步骤：

[![显示门户中 "Cloud Shell" 窗口的屏幕截图](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>在本地安装 CLI

也可在本地安装和使用 Azure CLI。 本操作指南文章要求运行 Azure CLI 版本2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

---

## <a name="sign-in-to-azure"></a>登录 Azure

# <a name="portal"></a>[门户](#tab/azure-portal)

登录 [Azure 门户](https://portal.azure.com)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 `Connect-AzAccount` 命令登录到 Azure 订阅，然后按照屏幕上的说明进行身份验证。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要启动 Azure Cloud Shell，请登录到[Azure 门户](https://portal.azure.com)。

若要登录到本地安装的 CLI，请运行 [az login](/cli/azure/reference-index#az-login) 命令：

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>创建 BlockBlobStorage 帐户

## <a name="portal"></a>[门户](#tab/azure-portal)
若要在 Azure 门户中创建 BlockBlobStorage 帐户，请按照以下步骤操作：

1. 在 Azure 门户中，选择 "**所有服务**" **> 存储 "** 类别 >**存储帐户**。

2. 在 "**存储帐户**" 下，选择 "**添加**"。

3. 在 "**订阅**" 字段中，选择要在其中创建存储帐户的订阅。

4. 在 "**资源组**" 字段中，选择现有资源组，或选择 "**新建"，** 然后输入新资源组的名称。

5. 在 "**存储帐户名称**" 字段中，输入帐户的名称。 请注意以下准则：

   - 该名称在 Azure 中必须唯一。
   - 名称长度必须介于3到24个字符之间。
   - 名称只能包含数字和小写字母。

6. 在 "**位置**" 字段中，选择存储帐户的位置，或使用默认位置。

7. 对于其余设置，请配置以下各项：

   |字段     |值  |
   |---------|---------|
   |**性能**    |  选择 "**高级**"。   |
   |**帐户种类**    | 选择**BlockBlobStorage**。      |
   |**复制**    |  保留 "**本地冗余存储（LRS）**" 的默认设置。      |

   ![显示用于创建块 blob 存储帐户的门户 UI](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. 选择“高级”选项卡****。

9. 如果要为数据分析优化存储帐户，请将 "**分层命名空间**" 设置为 "**已启用**"。 否则，请将此选项设置为默认值。

   若要了解详细信息，请参阅[Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 块 blob 存储帐户中的分层命名空间功能以公共预览版提供，可在美国东部、美国东部2、美国中部、美国中南部、美国西部2、英国南部、加拿大中部和澳大利亚东部地区获得。 若要查看限制，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)和[已知问题](data-lake-storage-known-issues.md)。 若要注册预览版，请参阅[此窗体](https://aka.ms/adlspremiumonboard)。

8. 选择 "查看" " **+ 创建**"，查看存储帐户设置。

9. 选择“创建”  。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行）。

2. 运行以下命令，确保已安装最新版本的 `Az` PowerShell 模块。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. 打开新的 PowerShell 控制台，并使用 Azure 帐户登录。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. 如果需要，请创建新的资源组。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. 创建 BlockBlobStorage 帐户。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   如果要为数据分析优化存储帐户，请将添加 `-EnableHierarchicalNamespace $True` 到命令。 若要了解详细信息，请参阅[Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 块 blob 存储帐户中的分层命名空间功能以公共预览版提供，可在美国东部、美国东部2、美国中部、美国中南部、美国西部2、英国南部、加拿大中部和澳大利亚东部地区获得。 若要查看限制，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)和[已知问题](data-lake-storage-known-issues.md)。 若要注册预览版，请参阅[此窗体](https://aka.ms/adlspremiumonboard)。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建块 blob 帐户，必须先安装 Azure CLI v。 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 登录到 Azure 订阅。

   ```azurecli
   az login
   ```

2. 如果需要，请创建新的资源组。 替换括号中的值（包括括号），并运行以下命令。

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. 创建 BlockBlobStorage 帐户。 替换括号中的值（包括括号），并运行以下命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   如果要为数据分析优化存储帐户，请将添加 `--hierarchical-namespace true` 到命令。 若要了解详细信息，请参阅[Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)。

   > [!NOTE]
   > 块 blob 存储帐户中的分层命名空间功能以公共预览版提供，可在美国东部、美国东部2、美国中部、美国中南部、美国西部2、英国南部、加拿大中部和澳大利亚东部地区获得。 若要查看限制，请参阅[Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)和[已知问题](data-lake-storage-known-issues.md)。 若要注册预览版，请参阅[此窗体](https://aka.ms/adlspremiumonboard)。
   
---

## <a name="next-steps"></a>后续步骤

- 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
