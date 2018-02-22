---
title: "Azure PowerShell 脚本示例 - 使用 Windows 版 AzCopy 跨存储帐户迁移 blob | Microsoft Docs"
description: "使用 AzCopy，将一个 Azure 存储帐户的 blob 内容复制到另一个。"
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>使用 Windows 版 AzCopy 跨存储帐户迁移 blob

本示例将所有 blob 对象从用户提供的源存储帐户迁移到用户提供的目标存储帐户。 

此操作通过使用 `Get-AzureStorageContainer` 命令实现，该命令将列出存储帐户中的所有容器。 然后，示例发出 AzCopy 命令，将每个容器从源存储帐户复制到目标存储帐户。 如果失败，示例将重试 $retryTimes 次（默认为 3，可通过 `-RetryTimes` 参数修改）。 如果每次重试都失败，用户可重新运行脚本，并使用 `-LastSuccessContainerName` 参数向示例提供上次成功复制的容器。 然后，示例将继续从该点复制容器。

本示例需要 Azure PowerShell 存储模块 4.0.2 或更高版本。 可使用 `Get-Module -ListAvailable Azure.storage` 检查安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

本示例需要最新版本的 [Windows 版 AzCopy](http://aka.ms/downloadazcopy)。 默认安装目录为 `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`。

本示例使用源存储帐户名称和键、目标存储帐户名称和键、AzCopy.exe 的完整文件路径（如果未安装在默认目录）。

本示例的输入示例如下：

如果 AzCopy 安装在默认目录：
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

如果 AzCopy 未安装在默认目录：

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

如果失败，则必须从特定容器重新运行该示例。 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>脚本说明

本脚本使用以下命令将数据从一个存储帐户复制到另一个。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | 返回与此存储帐户关联的容器。 |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | 创建 Azure 存储上下文。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

有关其他存储 PowerShell 脚本示例，可参阅 [Azure Blob 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
