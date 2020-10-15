---
title: Azure CLI - 在实验室中创建并验证虚拟机
description: 此 Azure CLI 脚本在实验室中创建虚拟机，并验证其是否可用。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: b64b6fa13bd0d320707dc279cb554d5c23f1d80d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88136161"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>使用 Azure CLI 在 Azure 开发测试实验室的实验室中创建虚拟机并验证其可用性

此 Azure CLI 脚本在实验室中创建虚拟机 (VM)。 使用 ssh 身份验证基于市场映像创建 VM。 然后该脚本会验证该 VM 是否可用。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az lab vm create](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | 在实验室中创建虚拟机 (VM)。 |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | 显示实验室中 VM 的状态。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 实验室服务 CLI 示例](../samples-cli.md)中找到其他 Azure 实验室服务 CLI 脚本示例。
