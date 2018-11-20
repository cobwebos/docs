---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 027b370d2497822dcbd6f3958556357957f9e8f5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50964656"
---
## <a name="sign-in-to-azure"></a>登录 Azure

运行 `Connect-AzureRmAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

```powershell
Connect-AzureRmAccount
```

如果你不知道要使用哪个位置，可以列出可用的位置。 使用以下代码示例显示位置列表，并找到要使用的位置。 本示例使用 **eastus**。 将位置存储在变量中，并使用该变量，这样就可以在一个位置更改它。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>创建存储帐户

使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 创建具有 LRS 复制功能的标准常规用途存储帐户。 接下来，获取用于定义要使用的存储帐户的存储帐户上下文。 对存储帐户执行操作时，引用上下文而不是重复传入凭据。 使用以下示例创建一个名为 mystorageaccount 的存储帐户，该帐户默认启用本地冗余存储 (LRS) 和 Blob 加密。

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `
  -Kind Storage

$ctx = $storageAccount.Context
```
