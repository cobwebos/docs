---
title: Azure CLI 脚本示例 - 部署托管应用程序
description: 提供 Azure CLI 示例脚本，该脚本将 Azure 托管应用程序定义部署到订阅。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: a9912f5134c3596740ef53f23531d57fe9467682
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497845"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>使用 Azure CLI 为服务目录部署托管应用程序

此脚本从服务目录部署托管应用程序定义。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令部署托管应用程序。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az-managedapp-create) | 创建托管应用程序。 提供模板的定义 ID 和参数。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
