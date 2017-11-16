---
title: "Azure 策略 json 示例 - 确保存储文件加密 | Microsoft Docs"
description: "此 json 示例策略要求对存储帐户启用文件加密。"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 922f9f6bf5bd8a84b2265fb0ea9c3b021bbe1621
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="ensure-storage-file-encryption"></a>确保存储文件加密

此策略要求对存储帐户启用文件加密。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>示例模板

[!code-json[main](../../../policy-templates/samples/Storage/storage-account-file-encryption/azurepolicy.json "Ensure storage file encryption")]

可将 [Azure 门户](#deploy-with-the-portal)与 [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 配合使用来部署此模板。

## <a name="deploy-with-the-portal"></a>使用门户进行部署

[![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FStorage%2Fstorage-account-file-encryption%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 进行部署

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "storage-account-file-encryption" -DisplayName "Ensure storage file encryption" -description "Ensures file encryption for storage accounts" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清理 PowerShell 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'storage-account-file-encryption' --display-name 'Ensure storage file encryption' --description 'Ensures file encryption for storage accounts' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "storage-account-file-encryption"
```

### <a name="clean-up-azure-cli-deployment"></a>清理 Azure CLI 部署

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

- 其他 Azure 策略模板示例位于 [Azure 策略模板](../json-samples.md)。
