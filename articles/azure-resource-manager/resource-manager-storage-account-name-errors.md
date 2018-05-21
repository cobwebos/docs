---
title: Azure 存储帐户名称错误 | Microsoft Docs
description: 说明指定存储帐户名称时可能遇到的错误。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: da7147439bba668c6c614c19d91a22ee78275c69
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-storage-account-names"></a>解决存储帐户名称错误

本文介绍了部署存储帐户时可能遇到的命名错误。

## <a name="symptom"></a>症状

如果存储帐户名称包含禁用的字符，则会收到如下错误：

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

对于存储帐户，必须为此资源提供一个在 Azure 中唯一的名称。 如果不提供唯一名称，则会收到如下所示的错误：

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

如果部署的存储帐户与订阅中的现有存储帐户名称相同，但提供一个不同的位置，则会收到一个错误消息，指示该存储帐户已存在于其他位置。 请删除现有存储帐户，或提供与现有存储帐户相同的位置。

## <a name="cause"></a>原因

存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。 此名称必须唯一。

## <a name="solution"></a>解决方案

请确保存储帐户名称是唯一的。 可将命名约定与 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函数的结果连接起来创建一个唯一名称。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

请确保存储帐户名称不超过 24 个字符。 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函数返回 13 个字符。 如果将前缀或后缀连接到 **uniqueString** 结果，请提供 11 个字符（或更少字符）的值。

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

请确保存储帐户名称不包含任何大写字母或特殊字符。