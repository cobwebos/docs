---
title: PowerShell 脚本：创建 Azure 数据共享邀请 |Microsoft Docs
description: 此 PowerShell 脚本发送数据共享邀请。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 220677df3bd0c221f61efd28a80b05cfd76e2734
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086636"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 脚本监视发送的数据共享的使用情况

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

| 命令 | 注释 |
|---|---|
| [新-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | 创建数据共享邀请。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在[Azure 数据共享 powershell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 powershell 脚本示例。
