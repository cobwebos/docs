---
title: 示例 - 没有与 Express Route 网络对等互连的网络
description: 此示例策略禁止将网络对等互联关联到指定资源组中的网络。
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 7e74f1b276decd14c48f7a02f00e682662282c8a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317566"
---
# <a name="no-network-peering-to-express-route-network"></a>没有与 Express Route 网络对等互连的网络

此策略禁止将网络对等互联关联到指定资源组中的网络。 用于防止与中心托管的网络基础结构连接。 指定要防止关联的资源组的名称。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板

[!code-json[main](../../../../policy-templates/samples/Network/no-network-peerings-to-er-network/azurepolicy.json "No network peering to ER network")]

可将 [Azure 门户](#deploy-with-the-portal)与 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 配合使用来部署此模板。

## <a name="deploy-with-the-portal"></a>使用门户进行部署

[![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fno-network-peerings-to-er-network%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name "no-network-peerings-to-er-network" -DisplayName "No network peering to ER network" -description "No network peering can be associated to networks in network in a resource group containing central managed network infrastructure." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -resourceGroupName <ER Network Resource Group Name> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清理 PowerShell 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'no-network-peerings-to-er-network' --display-name 'No network peering to ER network' --description 'No network peering can be associated to networks in network in a resource group containing central managed network infrastructure.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/no-network-peerings-to-er-network/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "no-network-peerings-to-er-network"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](index.md)中查看更多示例