---
title: 使用 Azure Stack 策略模块 | Microsoft Docs
description: 了解如何限制 Azure 订阅使其行为像 Azure Stack 订阅
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>使用 Azure Stack 策略模块管理 Azure 策略

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Azure Stack 策略模块，可为 Azure 订阅配置与 Azure Stack 相同的版本控制和服务可用性。  该模块使用 **New-AzureRMPolicyAssignment** cmdlet 创建 Azure 策略，限制订阅中可用的资源类型和服务。  配置策略之后，可以使用你的 Azure 订阅若要开发针对 Azure 堆栈的应用。

## <a name="install-the-module"></a>安装模块

1. 按照[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 步骤 1 中的说明，安装所需的 AzureRM PowerShell 模块版本。
2. [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
3. [配置适用于 Azure Stack 的 PowerShell](azure-stack-powershell-configure-user.md)

4. 导入 AzureStack.Policy.psm1 模块：

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>将策略应用到的 Azure 订阅

以下命令可用于针对 Azure 订阅中应用默认 Azure 堆栈策略。 在运行此命令之前, 替换*Azure 订阅名称*与 Azure 订阅。

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>将策略应用于资源组

你可能想要应用中更为具体的策略。 例如，你可能必须运行相同的订阅中的其他资源。 可以将策略应用范围限定为特定资源组，这样就可以使用 Azure 资源测试 Azure Stack 的应用。 在运行以下命令之前, 替换*Azure 订阅名称*替换为你的 Azure 订阅名称。

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>执行中的策略

部署 Azure 策略后，当你尝试部署策略禁止的资源时会收到错误。

![由于策略约束而资源部署失败的结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
