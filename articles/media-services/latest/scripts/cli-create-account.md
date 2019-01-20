---
title: 创建 Azure 媒体服务帐户 - Azure CLI | Microsoft Docs
description: 使用 Azure CLI 脚本创建 Azure 媒体服务帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 2eeb47c2e0f96eca1ca9b852a2be6ca3102ba71e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353339"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI 示例：创建 Azure 媒体服务帐户

本主题中的 Azure CLI 脚本演示如何创建 Azure 媒体服务帐户。 媒体服务帐户和与其关联的存储帐户必须是同一数据中心和同一资源组的一部分。

## <a name="prerequisites"></a>先决条件 

在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | 创建媒体服务帐户。 |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | 通过密码创建服务主体并配置其对 Azure 媒体服务帐户的访问权限。 
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |


## <a name="next-steps"></a>后续步骤

有关详细示例，请参阅 [Azure CLI 示例](../cli-samples.md)。
