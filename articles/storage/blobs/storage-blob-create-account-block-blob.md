---
title: 创建块 blob 存储帐户-Azure 存储 |Microsoft Docs
description: 演示如何创建具有高级性能特征的 Azure BlockBlobStorage 帐户。
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881866"
---
# <a name="create-a-blockblobstorage-account"></a>创建 BlockBlobStorage 帐户

BlockBlobStorage 帐户类型允许你创建具有高级性能特征的块 blob。 此类型的存储帐户针对具有高事务速率或需要极快访问时间的工作负荷进行了优化。 本文介绍如何使用 Azure 门户、Azure CLI 或 Azure PowerShell 创建 BlockBlobStorage 帐户。

有关 BlockBlobStorage 帐户的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
若要在 Azure 门户中创建 BlockBlobStorage 帐户，请按照以下步骤操作：

1. 在 Azure 门户中，选择 "**所有服务**" **> 存储 "** 类别 >**存储帐户**。

1. 在 "**存储帐户**" 下，选择 "**添加**"。

1. 在 "**订阅**" 字段中，选择要在其中创建存储帐户的订阅。

1. 在 "**资源组**" 字段中，选择现有资源组，或选择 "**新建"，** 然后输入新资源组的名称。

1. 在 "**存储帐户名称**" 字段中，输入帐户的名称。 请注意以下准则：

   - 该名称在 Azure 中必须唯一。
   - 名称长度必须介于3到24个字符之间。
   - 名称只能包含数字和小写字母。

1. 在 "**位置**" 字段中，选择存储帐户的位置，或使用默认位置。

1. 对于其余设置，请配置以下各项：

   |字段     |Value  |
   |---------|---------|
   |**性能**    |  选择 "**高级**"。   |
   |**帐户种类**    | 选择**BlockBlobStorage**。      |
   |**复制**    |  保留 "**本地冗余存储（LRS）** " 的默认设置。      |

   ![显示用于创建块 blob 存储帐户的门户 UI](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. 选择 "查看" " **+ 创建**"，查看存储帐户设置。

1. 选择**创建**。

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行）。

1. 运行以下命令，确保已安装 `Az` PowerShell 模块的最新版本。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. 打开新的 PowerShell 控制台，并使用 Azure 帐户登录。

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. 如果需要，请创建新的资源组。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. 创建 BlockBlobStorage 帐户。 替换引号中的值，并运行以下命令。

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建块 blob 帐户，必须先安装 Azure CLI v。 2.0.46 或更高版本。 可以运行 `az --version` 来查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

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

1. 创建 BlockBlobStorage 帐户。 替换括号中的值（包括括号），并运行以下命令。

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>后续步骤

- 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

- 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
