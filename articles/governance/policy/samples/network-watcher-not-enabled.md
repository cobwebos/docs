---
title: 示例 - 如果未对区域启用网络观察程序，则进行审核
description: 如果未对指定区域启用网络观察程序，则此示例策略定义会进行审核
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 7614b000f69eb74c83084aec0849a2741c82cd5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268256"
---
# <a name="sample---audit-if-network-watcher-is-not-enabled-for-region"></a>示例 - 如果未对区域启用网络观察程序，则进行审核

如果未对指定区域启用网络观察程序，则此策略会进行审核。 指定区域的名称以检查是否启用了网络观察程序。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板

[!code-json[main](../../../../policy-templates/samples/Network/audit-network-watcher-existence/azurepolicy.json "Audit if Network Watcher is not enabled for region")]

可将 [Azure 门户](#deploy-with-the-portal)与 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 配合使用来部署此模板。

## <a name="deploy-with-the-portal"></a>使用门户进行部署

[![将策略示例部署到 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Faudit-network-watcher-existence%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-network-watcher-existence" -DisplayName "Audit if Network Watcher is not enabled for region" -description "This policy audits if Network Watcher is not enabled for a selected region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -location <Audit if Network Watcher is not enabled for region> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清理 PowerShell 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-network-watcher-existence' --display-name 'Audit if Network Watcher is not enabled for region' --description 'This policy audits if Network Watcher is not enabled for a selected region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/audit-network-watcher-existence/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-network-watcher-existence"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例