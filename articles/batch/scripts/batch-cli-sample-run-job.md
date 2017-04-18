---
title: "Azure CLI 脚本示例 - 使用批处理运行作业 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 使用批处理运行作业"
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
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>使用 Azure CLI 在 Azure 批处理上运行作业

此脚本将创建一个批处理作业，并将一系列任务添加到该作业。 它还演示了如何监视作业及其任务。
运行此脚本时假定已设置批处理帐户，并且已配置池和应用程序。 有关详细信息，请参阅涵盖上述每个主题的[示例脚本](../batch-cli-samples.md)。

如果需要，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 登录到 Azure。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/batch/run-job/run-job.sh "运行作业")]

## <a name="clean-up-job"></a>清除作业

运行上述示例脚本后，可运行以下命令以删除作业及其所有任务。 请注意，将需要单独删除池；请参阅[有关管理池的教程](./batch-cli-sample-manage-pool.md)。

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建批处理作业和任务。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 针对批处理帐户进行身份验证。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | 创建批处理作业。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | 更新批处理作业的属性。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | 检索指定批处理作业的详细信息。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | 将任务添加到指定的批处理作业。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | 从指定的批处理作业中检索任务的详细信息。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 批处理 CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。

