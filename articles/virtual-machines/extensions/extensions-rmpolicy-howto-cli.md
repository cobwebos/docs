---
title: 使用 Azure Policy 限制 VM 扩展安装 | Microsoft Docs
description: 使用 Azure Policy 限制 VM 扩展部署。
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: f11d08e56fe1970aa7a0d82f368eb73589ece2fe
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412293"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>使用 Azure Policy 限制 Linux VM 上的扩展安装

如果想要阻止在 Linux VM 上使用或安装某些扩展，可以使用 CLI 创建 Azure Policy 以限制资源组中的 VM 扩展。 

本教程在 Azure Cloud Shell 中使用 CLI，后者已不断更新到最新版本。 如果要在本地运行 Azure CLI，则需要安装版本 2.0.26 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-rules-file"></a>创建规则文件

若要限制可以安装哪些扩展，需要使用[规则](/azure/azure-policy/policy-definition#policy-rule)来提供用于识别扩展的逻辑。

本示例演示如何通过在 Azure Cloud Shell 中创建规则文件来拒绝安装“Microsoft.OSTCExtensions”发布的扩展，但如果在本地使用 CLI，也可以创建一个本地文件并将路径 (~/clouddrive) 替换为计算机上本地文件的路径。

在 [bash Cloud Shell](https://shell.azure.com/bash) 中，键入：

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

将以下 .json 的内容复制并粘贴到该文件。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

完成后，按 **Esc** 键，然后键入 **:wq** 保存并关闭该文件。


## <a name="create-a-parameters-file"></a>创建参数文件

还需要一个[参数](/azure/azure-policy/policy-definition#parameters)文件，以创建一个用于传入要阻止的扩展列表的结构。 

本示例演示如何在 Cloud Shell 中为 Linux VM 创建参数文件，但如果在本地使用 CLI，也可以创建一个本地文件并将路径 (~/clouddrive) 替换为计算机上本地文件的路径。

在 [bash Cloud Shell](https://shell.azure.com/bash) 中，键入：

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

将以下 .json 的内容复制并粘贴到该文件。

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

完成后，按 **Esc** 键，然后键入 **:wq** 保存并关闭该文件。

## <a name="create-the-policy"></a>创建策略

策略定义是用于存储想要使用的配置的对象。 策略定义使用规则和参数文件定义策略。 使用 [az policy definition create](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create) 创建策略定义。

在此示例中，规则和参数是在 cloud shell 中创建并存储为 .json 文件的文件。

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>分配策略

此示例使用 [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) 将策略分配给资源组。 **myResourceGroup** 资源组中创建的任何 VM 将不能安装适用于 Linux 的 Linux VM 访问扩展或自定义脚本扩展。 该资源组必须存在，然后才能分配策略。

使用 [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) 获取你的订阅 ID 以替换示例中的订阅 ID。


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>测试策略

可通过创建新的 VM 并尝试添加新用户来测试策略。


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

尝试使用 VM 访问扩展创建一个名为 **myNewUser** 的新用户。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>删除分配

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>删除策略

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Azure Policy](../../azure-policy/azure-policy-introduction.md)。
