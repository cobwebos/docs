---
title: "部署模板使用 Azure 堆栈中的 PowerShell |Microsoft 文档"
description: "了解如何部署使用资源管理器模板和 PowerShell 的虚拟机。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>将使用 PowerShell 的 Azure 堆栈中的模板部署

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用 PowerShell 将 Azure 资源管理器模板部署到 Azure 堆栈开发工具包。  资源管理器模板部署和设置在单个协调的操作的应用程序的所有资源。

## <a name="run-azurerm-powershell-cmdlets"></a>运行 AzureRM PowerShell cmdlet
在此示例中，你运行脚本以将虚拟机部署到 Azure 堆栈开发工具包使用资源管理器模板。  在继续之前，确保你具有[配置 PowerShell](azure-stack-powershell-configure-user.md)  

在此示例模板中使用的 VHD 是 windows Server 2012 R2 Datacenter。

1. 转到<http://aka.ms/AzureStackGitHub>，搜索**101 简单 windows vm**模板，并将其保存到以下位置： c:\\模板\\azuredeploy-101-简单-windows-vm.json。
2. 在 PowerShell 中，运行以下的部署脚本。 替换*用户名*和*密码*使用用户名和密码。 在以后使用，递增的值*$myNum*参数，以避免覆盖您的部署。
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. 打开 Azure 堆栈门户中，单击**浏览**，单击**虚拟机**，并查找新的虚拟机 (*myDeployment001*)。


## <a name="next-steps"></a>后续步骤
[通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)

