---
title: Azure CLI 脚本示例 - 创建转换 | Microsoft Docs
description: 使用 Azure CLI 脚本创建转换。
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
ms.openlocfilehash: 32dcbce86153034048627692dddc14a2f5b3d129
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615190"
---
# <a name="cli-example-create-a-transform"></a>CLI 示例：创建转换

本文中的 Azure CLI 脚本演示如何创建转换。 转换描述了处理视频或音频文件的任务的简单工作流（通常被称作“脚本”）。 应始终检查具有所需名称和“脚本”的转换是否已存在。 如果已存在，应再次使用该转换。

## <a name="prerequisites"></a>先决条件 

- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

- [创建媒体服务帐户](../create-account-cli-how-to.md)。

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>后续步骤

有关详细示例，请参阅 [Azure CLI 示例](../cli-samples.md)。
