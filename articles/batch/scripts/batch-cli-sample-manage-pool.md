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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>使用 Azure CLI 管理 Azure Batch 池

这些脚本演示了 Azure CLI 中一些可用于在 Azure Batch 服务中创建和管理计算节点池的工具。

> [!NOTE]
> 本示例中的命令创建 Azure 虚拟机。 运行中的 VM 会在帐户中产生费用。 若要尽可能减少这些费用，请在运行完本示例后删除 VM。 请参阅[清理池](#clean-up-pools)。

可通过两种方式配置批处理池：使用云服务配置（仅适用于 Windows），或使用虚拟机配置（适用于 Windows 和 Linux）。 以下示例脚本显示如何使用这两个配置创建池。

## <a name="prerequisites"></a>先决条件

- 按照 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中提供的说明安装 Azure CLI（如果尚未这样做）。
- 创建 Batch 帐户（如果还没有帐户）。 有关创建帐户的示例脚本，请参阅[使用 Azure CLI 创建 Batch 帐户](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)。
- 将应用程序配置为从启动任务运行（如果尚未这样做）。 有关用于创建应用程序并将应用程序包上传到 Azure 的示例脚本，请参阅[使用 Azure CLI 将应用程序添加到 Azure Batch](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)。

## <a name="pool-with-cloud-service-configuration-sample-script"></a>使用云服务配置来配置池的示例脚本

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>使用虚拟机配置来配置池的示例脚本

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

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
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | 针对批处理帐户进行身份验证。  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | 列出批处理帐户中可用的应用程序。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | 创建 VM 池。  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | 更新池的属性。  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | 显示池的属性。  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | 删除指定的池。  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | 对池禁用自动缩放。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | 从指定的池中删除列出的节点。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Batch CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。


