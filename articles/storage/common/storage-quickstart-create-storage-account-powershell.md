---
title: "Azure 快速入门 - 使用 PowerShell 创建存储帐户 | Microsoft Docs"
description: "快速了解如何使用 PowerShell 新建存储帐户"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: tamram
ms.openlocfilehash: b4b917adfb3644cca71b6696df005fbf9e295240
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-powershell"></a>使用 PowerShell 创建存储帐户

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 此指南详细介绍如何使用 PowerShell 创建 Azure 存储帐户。 

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本快速入门需要 Azure PowerShell 模块 3.6 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

如果你不知道要使用哪个位置，可以列出可用的位置。 显示列表后，找到要使用的位置。 本示例使用 eastus。 将其存储在变量中，并使用该变量，这样就可以在某个位置更改它。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>创建通用的标准存储帐户

存储帐户分为几种类型，具体取决于其使用方式和要用于哪些服务（blob、文件、表或队列）。 下表显示可能的类型。

|存储帐户的类型|通用标准|通用高级|Blob 存储（热访问层和冷访问层）|
|-----|-----|-----|-----|
|支持的服务| Blob、文件、表和队列服务 | Blob 服务 | Blob 服务|
|支持的 Blob 类型|块 Blob、页 Blob 和追加 Blob | 页 Blob | 块 Blob 和追加 Blob|

使用[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 创建可用于四种服务的通用标准存储帐户。 将存储帐户命名为 *contosomvcstandard*，并将其配置为启用本地冗余存储和 blob 加密。

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。 在这种情况下，它会删除你创建的存储帐户。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，已创建一个通用的标准存储帐户。 若要了解如何使用存储帐户上传和下载 blob，请继续阅读 Blob 存储快速入门。
> [!div class="nextstepaction"]
> [使用 PowerShell 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象](../blobs/storage-quickstart-blobs-powershell.md)