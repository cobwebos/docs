---
title: 需要在 Azure 存储中安全传输 | Microsoft Docs
description: 了解 Azure 存储的“需要安全传输”功能，以及如何启用它。
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 0ec36864377d724795197851341cbb837f47c4c6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="require-secure-transfer-in-azure-storage"></a>在 Azure 存储中需要安全传输

“需要安全传输”选项通过仅允许来自安全连接的帐户请求，增强存储帐户安全性。 例如，在调用 REST API 访问存储帐户时，必须使用 HTTPS 进行连接。 “需要安全传输”拒绝使用 HTTP 的请求。

使用 Azure 文件服务时，如果启用了“需要安全传输”，任何未加密的连接都会失败。 这包括使用 SMB 2.1、未加密的 SMB 3.0 以及某些版本的 Linux SMB 客户端的方案。 

默认情况下，将禁用“需要安全传输”选项。

> [!NOTE]
> 由于 Azure 存储对自定义域名不支持 HTTPS，因此使用自定义域名时不应用此选项。 不支持经典存储帐户。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>在 Azure 门户中启用“需要安全传输”

在 [Azure 门户](https://portal.azure.com)中创建存储帐户时，可启用“需要安全传输”设置。 也可以为现有存储帐户启用该设置。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>新的存储帐户需要安全传输

1. 在 Azure 门户中打开“创建存储帐户”窗格。
1. 在“需要安全传输”下，选择“启用”。

  ![“创建存储帐户”边栏选项卡](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>对现有存储帐户需要安全传输

1. 在 Azure 门户中选择现有存储帐户。
1. 在存储帐户菜单窗格的“设置”下，选择“配置”。
1. 在“需要安全传输”下，选择“启用”。

  ![“存储帐户”菜单窗格](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>以编程方式启用“需要安全传输”

若要以编程方式启用“需要安全传输”，请通过 REST API、工具或库使用存储帐户属性中的 supportsHttpsTrafficOnly 设置：

* [REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts)（版本：2016-12-01）
* [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)（版本：4.1.0）
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)（版本：2.0.11）
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)（版本：1.1.0）
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)（版本：6.3.0）
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)（版本：1.1.0）
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)（版本： 0.11.0）

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>使用 PowerShell 启用“需要安全传输”设置

本示例需要 Azure PowerShell 模块 4.1 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

运行 `Connect-AzureRmAccount`，创建与 Azure 的连接。

 使用以下命令行检查该设置：

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

使用以下命令行启用该设置：

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>使用 CLI 启用“需要安全传输”设置

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 使用以下命令行检查该设置：

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

使用以下命令行启用该设置：

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>后续步骤
Azure 存储提供一整套安全性功能，这些功能相辅相成，可让开发人员共同构建安全的应用程序。 有关详细信息，请转到[存储安全指南](storage-security-guide.md)。
