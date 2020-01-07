---
title: 获取托管资源组并调整 VM 大小 - Azure PowerShell
description: 提供 Azure PowerShell 示例脚本，该脚本获取 Azure 托管应用程序的托管资源组。 该脚本重设 VM 大小。
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648954"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>使用 PowerShell 获取托管资源组中的资源并重设 VM 大小

此脚本从托管资源组中检索资源，并重设该资源组中 VM 的大小。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令部署托管应用程序。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | 列出托管应用程序。 提供要重点关注结果的资源组名称。 |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | 列出资源。 提供要重点关注结果的资源组和资源类型。 |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | 更新虚拟机的大小。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
