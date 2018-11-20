---
title: Azure CLI 脚本示例 - 创建 Azure 媒体服务资产 | Microsoft Docs
description: 本主题中的 Azure CLI 脚本演示如何创建媒体服务资产供上传内容使用。
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 76d2adf3c408e174081c3106e07a87f751b4a179
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612655"
---
# <a name="cli-example-create-an-asset"></a>CLI 示例：创建资产

本文中的 Azure CLI 脚本演示如何创建媒体服务资产供上传内容使用。

## <a name="prerequisites"></a>先决条件 

- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

- [创建媒体服务帐户](../create-account-cli-how-to.md)。

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>后续步骤

有关详细示例，请参阅 [Azure CLI 示例](../cli-samples.md)。
