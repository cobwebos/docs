---
title: "Azure PowerShell 脚本示例 - 创建托管应用程序定义 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 创建托管应用程序定义"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-powershell"></a>使用 PowerShell 创建托管应用程序定义

此脚本会将托管应用程序定义发布到服务目录。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建托管应用程序定义。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | 创建托管应用程序定义。 提供包含所需文件的包。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
