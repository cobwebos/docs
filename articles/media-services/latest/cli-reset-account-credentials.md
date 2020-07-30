---
title: Azure CLI 脚本示例 - 重置帐户凭据 | Microsoft Docs
description: 使用 Azure CLI 脚本重置帐户凭据和恢复 app.config 设置。
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
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 0a1a5bf9f192a7128da8843b79b60be0175cec23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092127"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI 示例：重置帐户凭据

本文中的 Azure CLI 脚本演示如何重置帐户凭据和恢复 app.config 设置。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./create-account-howto.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>示例脚本

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>后续步骤

* [az ams](/cli/azure/ams)
* [重置凭据](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
