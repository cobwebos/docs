---
title: 使用 Azure Stack 策略模块 | Microsoft Docs
description: 了解如何限制 Azure 订阅使其行为像 Azure Stack 订阅
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678519"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>使用 Azure Stack 策略模块管理 Azure Policy

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Azure Stack 策略模块，可为 Azure 订阅配置与 Azure Stack 相同的版本控制和服务可用性。 该模块使用[New-azurermpolicydefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet 来创建 Azure 策略限制的资源类型和在订阅中可用的服务。 然后通过创建策略分配相应的作用域内[New-azurermpolicyassignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet。 配置策略后，可以使用 Azure 订阅来开发针对 Azure Stack 的应用。

## <a name="install-the-module"></a>安装模块

1. 安装所需的版本的 AzureRM PowerShell 模块中的步骤 1 中所述[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
2. [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)。
3. [配置适用于 Azure Stack 的 PowerShell](azure-stack-powershell-configure-user.md)。
4. 导入 AzureStack.Policy.psm1 模块：

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>将策略应用于 Azure 订阅

可以使用以下命令针对 Azure 订阅应用默认 Azure Stack 策略。 运行此命令之前，将为`Azure Subscription Name`与你的 Azure 订阅的名称。

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>将策略应用于资源组

你可能想要应用更细化的策略。 例如，你在相同的订阅中可能有其他正在运行的资源。 可以将策略应用范围限定为特定资源组，这样就可以使用 Azure 资源测试 Azure Stack 的应用。 运行以下命令之前，将为`Azure Subscription Name`与你的 Azure 订阅的名称。

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>执行中的策略

部署 Azure 策略后，你会收到错误，当您尝试部署策略禁止的资源。

![由于策略约束而资源部署失败的结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
