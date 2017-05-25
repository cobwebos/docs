---
title: "Azure CLI 脚本示例 - 在批处理中添加应用程序 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 在批处理中添加应用程序"
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
ms.openlocfilehash: 5d057eaf32867aedc95d58c5185e2be1f9385ec0
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>使用 Azure CLI 将应用程序添加到 Azure Batch

此脚本演示如何设置要与 Azure Batch 池或任务配合使用的应用程序。 若要设置应用程序，请将可执行文件与所有依赖文件一起打包为 .zip 文件。 在此示例中，可执行 zip 文件名为“my-application-exe.zip”。

## <a name="prerequisites"></a>先决条件

- 按照 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中提供的说明安装 Azure CLI（如果尚未这样做）。
- 创建 Batch 帐户（如果还没有帐户）。 有关创建帐户的示例脚本，请参阅[使用 Azure CLI 创建 Batch 帐户](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/batch/add-application/add-application.sh "添加应用程序")]

## <a name="clean-up-application"></a>清除应用程序

运行上述示例脚本后，可运行以下命令以删除应用程序及其已上载的应用程序包。

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建应用程序并上载应用程序包。
表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | 创建应用程序。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | 更新应用程序的属性。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | 将应用程序包添加到指定的应用程序。  |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Batch CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。

