---
title: PowerShell 脚本：接受来自 Azure 数据共享的邀请 |Microsoft Docs
description: 此 PowerShell 脚本接受来自现有数据共享的邀请。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307332"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>使用 PowerShell 接受数据共享邀请

此 PowerShell 脚本接受发送到使用者的邀请。

## <a name="sample-script"></a>示例脚本
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| Command | 说明 |
|---|---|
| [AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 获取和列出已发送的数据共享邀请。 |
| [新-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | 创建数据共享订阅。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在[Azure 数据共享 powershell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 powershell 脚本示例。

