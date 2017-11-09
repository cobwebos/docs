---
title: "使用 Azure 堆栈策略模块 |Microsoft 文档"
description: "了解如何约束表现得像 Azure 堆栈订阅的 Azure 订阅"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e505c52a5e1897d5626ee2cacce9fa3eff12fbbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>管理 Azure 策略使用 Azure 堆栈策略模块

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈策略模块，可使用相同的版本控制和 Azure 堆栈的服务可用性配置一个 Azure 订阅。  模块使用**新建 AzureRMPolicyAssignment** cmdlet 可创建 Azure 的策略，这可以限制的资源类型和订阅中可用的服务。  完成后，你可以使用你的 Azure 订阅若要开发针对 Azure 堆栈的应用。  

## <a name="install-the-module"></a>安装模块
1. 安装 AzureRM PowerShell 模块中，所需的版本的步骤 1 中所述[安装适用于 Azure 堆栈的 PowerShell](azure-stack-powershell-install.md)。   
2. [从 GitHub 下载 Azure 堆栈工具](azure-stack-powershell-download.md)  
3. [配置适用于 Azure Stack 的 PowerShell](azure-stack-powershell-configure-user.md)

4. 导入 AzureStack.Policy.psm1 模块：

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>将策略应用到订阅
可以使用以下命令，将针对你的 Azure 订阅的默认 Azure 堆栈策略。 在运行前，替换*Azure 订阅名称*与 Azure 订阅。

```PowerShell
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>将策略应用到资源组
你可能想要应用的更精细的方法中的策略。  例如，您可能必须运行相同的订阅中的其他资源。  您可以限定范围策略应用到特定资源组，这样就可以使用 Azure 资源的 Azure 堆栈测试你的应用。 在运行前，替换*Azure 订阅名称*替换为你的 Azure 订阅名称。

```PowerShell
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>在操作中的策略
后你部署 Azure 策略，你收到错误，当你尝试部署的资源的情况下禁止的策略。  

![由于策略约束的资源部署失败的结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤
[通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)

[使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)

[部署使用 Visual Studio 模板](azure-stack-deploy-template-visual-studio.md)
