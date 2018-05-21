---
title: 部署模板在 Azure 堆栈中使用 PowerShell |Microsoft 文档
description: 将模板部署到使用 PowerShell 的 Azure 堆栈。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>将模板部署到 Azure 堆栈使用 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 PowerShell，将 Azure 资源管理器模板部署到 Azure 堆栈。 这篇文章演示了如何使用 PowerShell 来部署模板。

## <a name="run-azurerm-powershell-cmdlets"></a>运行 AzureRM PowerShell cmdlet

此示例使用 AzureRM PowerShell cmdlet 和 GitHub 上存储的模板。 此模板创建 Windows Server 2012 R2 Datacenter 虚拟机。

>[!NOTE]
>尝试运行此示例之前，请确保你已[配置 PowerShell](azure-stack-powershell-configure-user.md) Azure 堆栈用户。

1. 转到<http://aka.ms/AzureStackGitHub>并查找**101 简单 windows vm**模板。 将模板保存到此位置： c:\\模板\\azuredeploy-101-简单-windows-vm.json。
2. 打开提升的 PowerShell 命令提示符。
3. 替换*用户名*和*密码*在以下脚本中与你的用户名和密码，以及然后运行该脚本。

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

   >[!IMPORTANT]
   >每次运行此脚本，递增的值的"$myNum"参数，以避免覆盖您的部署。

4. 打开 Azure 堆栈门户中，选择**浏览**，然后选择**虚拟机**若要查找新的虚拟机 (*myDeployment001*)。

## <a name="next-steps"></a>后续步骤

[通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
