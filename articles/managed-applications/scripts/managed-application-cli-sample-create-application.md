---
title: "Azure CLI 脚本示例 - 部署托管应用程序 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 部署托管应用程序定义"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 62d0247df3b3d9f242877e4ea27ccc871cf797c0
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>使用 Azure CLI 为服务目录部署托管应用程序

此脚本从服务目录部署托管应用程序定义。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令部署托管应用程序。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | 创建托管应用程序。 提供模板的定义 ID 和参数。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。
