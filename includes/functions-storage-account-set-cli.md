---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839031"
---
打开 local.settings.json 文件并复制 `AzureWebJobsStorage` 的值（即存储帐户连接字符串）。 使用以下 Bash 命令将 `AZURE_STORAGE_CONNECTION_STRING` 环境变量设置为该连接字符串：

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

在 `AZURE_STORAGE_CONNECTION_STRING` 环境变量中设置连接字符串时，无需每次都要提供身份验证，即可访问你的存储帐户。