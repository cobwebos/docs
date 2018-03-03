---
title: "在 Azure 堆栈中可用的进行虚拟机规模集 |Microsoft 文档"
description: "了解如何云操作员可以向 Azure 堆栈应用商店中添加虚拟机规模"
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: cb8ac5435b7a5c6deb9d4571696c79b2ed15c93a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure 堆栈中提供虚拟机规模集

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

虚拟机缩放集是 Azure 堆栈计算资源。 你可以使用它们来部署和管理一组相同的虚拟机。 对于所有虚拟机配置相同，不需要预设置的虚拟机缩放集。 它是更轻松地生成面向大计算、 大数据和容器化工作负荷的大规模服务。

本文将指导您完成使缩放集 Azure 堆栈应用商店中可用的过程。 完成此过程之后，你的用户可以将虚拟机规模集对它们的订阅添加。

Azure 堆栈上的虚拟机规模集就像在 Azure 上的虚拟机规模集。 有关详细信息，请参阅以下视频：
* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [Guy Bowerman 介绍虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure 堆栈上虚拟机规模集不支持自动缩放。 可以将多个实例添加到缩放集使用 Azure 堆栈门户、 资源管理器模板或 PowerShell。

## <a name="prerequisites"></a>先决条件
* **Powershell 和工具**

   安装和配置适用于 Azure 堆栈的 PowerShell 和 Azure 堆栈工具。 请参阅[获取启动并运行 Azure 堆栈中的 PowerShell](azure-stack-powershell-configure-quickstart.md)。

   安装 Azure 堆栈工具后，请确保你导入以下的 PowerShell 模块 (相对于的路径。 \ComputeAdmin 文件夹 AzureStack 工具主文件夹中的):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **操作系统映像**

   如果你尚未添加到 Azure 堆栈应用商店的操作系统映像，请参阅[将 Windows Server 2016 VM 映像添加到 Azure 堆栈 marketplace](azure-stack-add-default-image.md)。

   Linux 支持下载 Ubuntu Server 16.04 并将其使用添加```Add-AzsVMImage```使用以下参数： ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```。

## <a name="add-the-virtual-machine-scale-set"></a>添加虚拟机规模集

编辑你的环境的以下 PowerShell 脚本，然后运行它，以便添加到你的 Azure 堆栈 Marketplace 设置虚拟机规模。 

``$User`` 是用于连接管理员门户的帐户。 例如，serviceadmin@contoso.onmicrosoft.com。

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>删除虚拟机规模集

若要删除虚拟机缩放集库项，运行以下 PowerShell 命令：

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> 不能立即删除库项。 夜晚你需要刷新几次门户之前的项将显示删除从应用商店。


## <a name="next-steps"></a>接下来的步骤
[Azure 堆栈的常见问题](azure-stack-faq.md)

