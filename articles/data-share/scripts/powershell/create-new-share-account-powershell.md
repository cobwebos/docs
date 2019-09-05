---
title: PowerShell 脚本：创建新的 Azure 数据共享帐户 |Microsoft Docs
description: 此 PowerShell 脚本创建新的数据共享帐户。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307269"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>使用 PowerShell 在 Azure 中创建数据共享帐户

此 PowerShell 脚本创建新的数据共享帐户。 

## <a name="sample-script"></a>示例脚本

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| Command | 说明 |
|---|---|
| [新-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | 创建数据共享帐户。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在[Azure 数据共享 powershell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 powershell 脚本示例。
