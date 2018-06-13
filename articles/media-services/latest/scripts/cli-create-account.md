---
title: Azure CLI 脚本示例 - 创建 Azure 媒体服务帐户 | Microsoft Docs
description: 使用 Azure CLI 脚本创建 Azure 媒体服务帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 783dc0fd37f98a12d9240ad4b3ee72aa98212eff
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159707"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI 示例：创建 Azure 媒体服务帐户

本主题中的 Azure CLI 脚本演示如何创建 Azure 媒体服务帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 创建存储帐户。 |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | 创建媒体服务帐户。 |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | 通过密码创建服务主体并配置其对 Azure 媒体服务帐户的访问权限。 
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |


## <a name="next-steps"></a>后续步骤

有关详细示例，请参阅 [Azure CLI 示例](../cli-samples.md)。
