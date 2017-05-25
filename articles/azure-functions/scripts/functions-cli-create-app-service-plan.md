---
title: "Azure CLI 脚本示例 - 在应用服务计划中创建 Function App | Microsoft Docs"
description: "Azure CLI 脚本示例 - 在应用服务计划中创建 Function App"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 8c98e392c1c735458184bb782ed9c42f468f33de
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-in-an-app-service-plan"></a>在应用服务计划中创建 Function App

此示例脚本将创建一个 Azure Function App，作为函数的容器。 将使用专用应用服务计划创建 Function App，这意味着服务器资源将始终启用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

此脚本使用专用[应用服务计划](../functions-scale.md#app-service-plan)创建 Azure Function App。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "在应用服务计划中创建 Azure Function")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 创建 Azure 存储帐户。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | 创建应用服务计划。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | 创建 Azure Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

