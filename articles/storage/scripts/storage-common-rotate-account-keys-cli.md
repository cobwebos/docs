---
title: "Azure CLI 脚本示例 - 轮换存储帐户访问密钥 | Microsoft Docs"
description: "创建 Azure 存储帐户，然后检索并轮换其帐户访问密钥。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1b59144e0426b50c2ac49acbd7914d975ff037ec
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>创建存储帐户并轮换其帐户访问密钥

此脚本创建一个 Azure 存储帐户，显示新存储帐户的访问密钥，然后更新（轮换）密钥。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、存储帐户和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建存储帐户并检索和轮换其访问密钥。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#create) | 在指定资源组中创建 Azure 存储帐户。 |
| [az storage account keys list](/cli/azure/storage/account/keys#list) | 显示指定帐户的存储帐户访问密钥。 |
| [az storage account keys renew](/cli/azure/storage/account/keys#renew) | 重新生成主或辅助存储帐户访问密钥。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/overview)。

有关其他存储 CLI 脚本示例，可参阅 [Azure Blob 存储的 Azure CLI 示例](../blobs/storage-samples-blobs-cli.md)。

