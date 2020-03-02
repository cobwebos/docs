---
title: Azure CLI 脚本示例 - 删除 Azure 应用配置存储区
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本删除 Azure 应用配置存储
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 7f73de459d8ce9f74e3925789af630b7c804d605
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523566"
---
# <a name="delete-an-azure-app-configuration-store"></a>删除 Azure 应用配置存储区

此示例脚本删除 Azure 应用配置的实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令删除应用程序配置存储区。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | 删除应用程序配置存储区资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
