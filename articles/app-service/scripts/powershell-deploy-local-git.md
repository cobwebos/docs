---
title: PowerShell：从本地 Git 存储库进行部署
description: 了解如何使用 Azure PowerShell 自动部署和管理应用服务。 此示例演示如何从本地 Git 存储库部署代码。
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7355f2730cc54ca027e20c48e989e9f28dac7487
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684845"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>从本地 Git 存储库创建 Web 应用并部署代码

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后从本地 Git 存储库部署 Web 应用代码。

必要时，请遵照 [Azure PowerShell 指南](/powershell/azure/overview)中的说明更新到最新版本的 Azure PowerShell，并运行 `Connect-AzAccount` 来与 Azure 建立连接。 此外，需将应用程序代码提交到本地 Git 存储库。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 使用所需的资源组和应用服务组创建 Web 应用。 如果当前目录包含 Git 存储库，则还要添加 `azure` 远程控制。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
