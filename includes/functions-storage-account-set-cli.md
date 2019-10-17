---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329556"
---
### <a name="set-the-storage-account-connection"></a>设置存储帐户连接

打开 local.settings.json 文件并复制 `AzureWebJobsStorage` 的值（即存储帐户连接字符串）。 使用以下 Bash 命令将 `AZURE_STORAGE_CONNECTION_STRING` 环境变量设置为该连接字符串：

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

在 `AZURE_STORAGE_CONNECTION_STRING` 环境变量中设置连接字符串时，无需每次都要提供身份验证，即可访问你的存储帐户。