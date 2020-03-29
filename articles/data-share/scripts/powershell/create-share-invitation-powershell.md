---
title: PowerShell 脚本：创建 Azure 数据共享邀请*微软文档
description: 此 PowerShell 脚本发送数据共享邀请。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307229"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 监视已发送数据共享的使用情况

此 PowerShell 脚本创建数据共享邀请。

## <a name="sample-script"></a>示例脚本


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [新-阿兹数据共享邀请](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | 创建数据共享邀请。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

其他 Azure 数据共享 PowerShell 脚本示例可在[Azure 数据共享 PowerShell 示例中](../../samples-powershell.md)找到。
