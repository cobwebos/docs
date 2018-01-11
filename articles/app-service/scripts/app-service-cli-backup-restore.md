---
title: "Azure CLI 脚本示例 - 从备份恢复 Web 应用 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 从备份恢复 web 应用"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: bec915f3e321cf2422f7cc2cad3ce1dfb6b3317a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="restore-a-web-app-from-a-backup"></a>从备份中还原 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后为其创建一次性备份。 

若要运行此脚本，需要 Web 应用的现有备份。 若要创建备份，请参阅[备份 Web 应用](app-service-cli-backup-onetime.md)或[创建 Web 应用的计划备份](app-service-cli-backup-scheduled.md)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_list) | 获取 Web 应用的备份列表。 |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_restore) | 从备份中恢复 web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 应用服务文档](../app-service-cli-samples.md)中找到其他应用服务 CLI 脚本示例。
