---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203176"
---
## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Functions 使用通用帐户在 Azure 存储中保留状态以及有关函数的其他信息。 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令在创建的资源组中创建通用存储帐户。

在以下命令中，请将 `<storage_name>` 占位符替换成全局唯一存储帐户名。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
