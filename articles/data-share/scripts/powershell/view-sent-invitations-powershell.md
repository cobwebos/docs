---
title: PowerShell 脚本：列出发送到使用者的 Azure 数据共享邀请 |Microsoft Docs
description: 此 PowerShell 脚本接受来自现有数据共享的邀请。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 1304b478bd07ed61293b668badee56338a9cab5a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242900"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>使用 PowerShell 获取数据共享邀请

此 PowerShell 脚本将获取发送到使用者的邀请。

## <a name="sample-script"></a>示例脚本
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| Command | 说明 |
|---|---|
| [AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | 获取和列出已发送的数据共享邀请。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在[Azure 数据共享 powershell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 powershell 脚本示例。
