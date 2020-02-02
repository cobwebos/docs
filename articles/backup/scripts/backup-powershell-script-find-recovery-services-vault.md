---
title: PowerShell 脚本 - 查找存储帐户的保管库
description: 了解如何使用 Azure PowerShell 脚本查找存储帐户所注册到的恢复服务保管库。
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775863"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>用于查找存储帐户所注册到的恢复服务保管库的 Powershell 脚本

此脚本可帮助你查找存储帐户所注册到的恢复服务保管库。

## <a name="sample-script"></a>示例脚本

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>如何执行脚本

1. 在计算机上使用所选名称保存上述脚本。 在此示例中，我们将此脚本保存为 *FindRegisteredStorageAccount.ps1*。
2. 通过提供以下参数来执行脚本：

    * **-ResourceGroupName** - 存储帐户的资源组
    * **-StorageAccountName** - 存储帐户名称
    * **-SubscriptionID** - 存储帐户所在的订阅的 ID。

以下示例尝试查找 *afsaccount* 存储帐户所注册到的恢复服务保管库：

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>输出

输出将显示存储帐户所注册到的恢复服务保管库的完整路径。 下面是示例输出：

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>后续步骤

了解如何[从 Azure 门户备份 Azure 文件共享](https://docs.microsoft.com/azure/backup/backup-afs)
