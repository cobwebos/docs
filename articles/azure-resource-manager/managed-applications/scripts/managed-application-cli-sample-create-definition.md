---
title: 创建托管应用程序定义 - Azure CLI
description: 提供在订阅中创建托管应用程序定义的 Azure CLI 脚本示例。
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7feb00b581732cdc1956c4ac23af571180ff09e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497828"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>使用 Azure CLI 创建托管应用程序定义

此脚本会将托管应用程序定义发布到服务目录。 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建托管应用程序定义。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az-managedapp-definition-create) | 创建托管应用程序定义。 提供包含所需文件的包。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
