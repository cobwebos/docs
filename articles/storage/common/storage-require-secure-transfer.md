---
title: 需要安全传输以确保安全连接
titleSuffix: Azure Storage
description: 了解如何要求安全传输 Azure 存储的请求。 当你需要对存储帐户进行安全传输时，来自不安全连接的任何请求都将被拒绝。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3b2d78bd929e23d49a57f337022f6678114bb5fe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457440"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>需要安全传输以确保安全连接

可以通过为存储帐户设置 "**需要安全传输**" 属性，将存储帐户配置为仅接受来自安全连接的请求。 需要安全传输时，来自不安全连接的任何请求都将被拒绝。 Microsoft 建议你始终需要对所有存储帐户进行安全传输。

需要安全传输时，必须通过 HTTPS 对 Azure 存储 REST API 操作进行调用。 拒绝通过 HTTP 发出的任何请求。

如果存储帐户需要安全传输，则在不加密的情况下通过 SMB 连接到 Azure 文件共享会失败。 不安全连接的示例包括通过 SMB 2.1、不加密的 SMB 3.0 或某些版本的 Linux SMB 客户端进行的连接。

默认情况下，在 Azure 门户中创建存储帐户时，将启用 "**需要安全传输**" 属性。 但是，当你使用 SDK 创建存储帐户时，它会被禁用。

> [!NOTE]
> 由于 Azure 存储对自定义域名不支持 HTTPS，因此使用自定义域名时不应用此选项。 不支持经典存储帐户。

## <a name="require-secure-transfer-in-the-azure-portal"></a>需要安全传输 Azure 门户

在[Azure 门户](https://portal.azure.com)中创建存储帐户时，可以启用 "**需要安全传输**" 属性。 也可以为现有存储帐户启用该设置。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>新的存储帐户需要安全传输

1. 在 Azure 门户中打开“创建存储帐户”窗格。
1. 在“需要安全传输”下，选择“启用”。

   ![“创建存储帐户”边栏选项卡](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>对现有存储帐户需要安全传输

1. 在 Azure 门户中选择现有存储帐户。
1. 在存储帐户菜单窗格的“设置”下，选择“配置”。
1. 在“需要安全传输”下，选择“启用”。

   ![“存储帐户”菜单窗格](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>需要从代码进行安全传输

若要以编程方式要求安全传输，请设置存储帐户上的_supportsHttpsTrafficOnly_属性。 可以使用存储资源提供程序 REST API、客户端库或工具来设置此属性：

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>需要通过 PowerShell 进行安全传输

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本示例需要 Azure PowerShell 模块 Az 0.7 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

运行 `Connect-AzAccount`，创建与 Azure 的连接。

 使用以下命令行检查该设置：

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

使用以下命令行启用该设置：

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>需要安全传输 Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 使用以下命令检查设置：

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

使用以下命令启用此设置：

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>后续步骤

[Blob 存储的安全建议](../blobs/security-recommendations.md)
