---
title: "Azure CLI 脚本示例 - 管理批处理中的池 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 管理批处理中的池"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>使用 Azure CLI 管理 Azure 批处理池

这些脚本演示了 Azure CLI 中一些可用于在 Azure 批处理服务中创建和管理计算节点池的工具。

运行这些脚本的假设条件是，已设置批处理帐户，并且已配置应用程序。 有关详细信息，请参阅涵盖上述每个主题的[示例脚本](../batch-cli-samples.md)。

> [!NOTE]
> 本示例中的命令创建 Azure 虚拟机。 运行中的 VM 会在帐户中产生费用。 若要尽可能减少这些费用，请在运行完本示例后删除 VM。 请参阅[清理池](#clean-up-pools)。

如果需要，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 登录到 Azure。

可通过两种方式配置批处理池：使用云服务配置（仅适用于 Windows），或使用虚拟机配置（适用于 Windows 和 Linux）。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>使用云服务配置来配置池的示例脚本

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "管理云服务池")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>使用虚拟机配置来配置池的示例脚本

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "管理虚拟机池")]

## <a name="clean-up-pools"></a>清理池

运行上述示例脚本后，可运行以下命令删除池。
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建和操作批处理池。
表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 针对批处理帐户进行身份验证。  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | 列出批处理帐户中可用的应用程序。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | 创建 VM 池。  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | 更新池的属性。  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | 显示池的属性。  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | 删除指定的池。  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | 对池禁用自动缩放。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | 从指定的池中删除列出的节点。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 批处理 CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。


