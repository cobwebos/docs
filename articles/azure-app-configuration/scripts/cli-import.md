---
title: Azure CLI 脚本示例 - 导入到应用配置存储区
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本 - 将配置导入到 Azure 应用配置
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 71d6aafa82f647b9c6164ee9a06b43ed7e9a66af
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523578"
---
# <a name="import-to-an-azure-app-configuration-store"></a>导入 Azure 应用配置存储区

此示例脚本将密钥值设置导入 Azure 应用程序配置存储区。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 Azure CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 若要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令导入应用程序配置存储区。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | 导入应用程序配置存储区资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
