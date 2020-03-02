---
title: Azure CLI 脚本示例 - 在应用配置存储区中使用密钥值
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本在应用配置存储中创建、查看、更新和删除密钥值
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 1a4edabe666a554ccd01d110f0f71226221dfc67
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523637"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>处理 Azure 应用配置存储区中的密钥值

此示例脚本演示如何：
* 创建新的键值对
* 列出所有现有的键值对
* 更新新建的密钥的值
* 删除新的键值对

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

下表列出了我们的示例脚本中所用的命令。 

| Command | 说明 |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | 创建或更新键值对。 |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | 列出应用配置存储中的键值对。 |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | 删除键值对。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
