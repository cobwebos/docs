---
title: CLI：从备份中还原应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何从备份还原应用。
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: be6a0a5b700c4110acd4481b12130185c9878883
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962752"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>使用 CLI 从备份中还原 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后为其创建一次性备份。 

若要运行此脚本，需要 Web 应用的现有备份。 若要创建备份，请参阅[备份 Web 应用](cli-backup-onetime.md)或[创建 Web 应用的计划备份](cli-backup-scheduled.md)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | 获取 Web 应用的备份列表。 |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | 从备份中恢复 web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。