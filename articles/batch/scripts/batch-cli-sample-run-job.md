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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5fe1e3595d9459e60b2fd54d6f17f6822731f453
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>使用 Azure CLI 在 Azure Batch 上运行作业

此脚本将创建一个批处理作业，并将一系列任务添加到该作业。 它还演示了如何监视作业及其任务。 最后，它演示如何有效地查询 Batch 服务，以获取有关作业任务的信息。

## <a name="prerequisites"></a>先决条件

- 按照 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中提供的说明安装 Azure CLI（如果尚未这样做）。
- 创建 Batch 帐户（如果还没有帐户）。 有关创建帐户的示例脚本，请参阅[使用 Azure CLI 创建 Batch 帐户](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)。
- 将应用程序配置为从启动任务运行（如果尚未这样做）。 有关用于创建应用程序并将应用程序包上传到 Azure 的示例脚本，请参阅[使用 Azure CLI 将应用程序添加到 Azure Batch](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)。
- 配置将在其中运行作业的池。 有关用于使用云服务配置或虚拟机配置创建池的示例脚本，请参阅[使用 Azure CLI 管理 Azure Batch 池](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/batch/run-job/run-job.sh "运行作业")]

## <a name="clean-up-job"></a>清除作业

运行上述示例脚本后，可运行以下命令以删除作业及其所有任务。 请注意，池将需要单独删除。 有关创建和删除池的详细信息，请参阅[使用 Azure CLI 管理 Azure Batch 池](./batch-cli-sample-manage-pool.md)。

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
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#list) | 列出与指定的作业关联的任务。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Batch CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。

