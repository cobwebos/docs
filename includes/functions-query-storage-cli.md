---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329549"
---
### <a name="query-the-storage-queue"></a>查询存储队列

可以使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令查看帐户中的存储队列，如以下示例所示：

```azurecli-interactive
az storage queue list --output tsv
```

此命令的输出包含名为 `outqueue` 的队列，即运行函数时创建的队列。

接下来，使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令查看此队列中的消息，如以下示例所示：

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

返回的字符串应与发送的用于测试函数的消息相同。

> [!NOTE]  
> 以上示例从 base64 解码返回的字符串。 这是因为，队列存储绑定以 [base64 字符串](../articles/azure-functions/functions-bindings-storage-queue.md#encoding)的形式写入和读取 Azure 存储。