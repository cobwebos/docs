---
title: "Azure PowerShell 脚本示例 - 使用 FTP 将文件上传到 Web 应用 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 使用 FTP 将文件上传到 Web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 96b99110b63b037746fcc40eb15db5d718eb71a1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>使用 FTP 将文件上传到 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，并使用 FTP（通过 [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)）部署 Web 应用代码。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

主要使用 FTP 将文件上传到 Web 应用

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [Get-AzureRmWebAppPublishingProfile](/powershell/module/azurerm.websites/get-azurermwebapppublishingprofile) | 获取 Web 应用的发布配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../app-service-powershell-samples.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
