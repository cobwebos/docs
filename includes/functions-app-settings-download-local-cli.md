---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839139"
---
你已在 Azure 中创建了一个函数应用以及所需的存储帐户。 此帐户的连接字符串安全存储在 Azure 中的应用设置内。 在本文中，你要将消息写入到同一帐户中的存储队列。 若要在本地运行函数时连接到该存储帐户，必须将应用设置下载到 local.settings.json 文件。 运行以下 Azure Functions Core Tools 命令，将设置下载到 local.settings.json（请将 `<APP_NAME>` 替换为前一篇文章中的函数应用名称）：

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

可能需要登录到 Azure 帐户。

> [!IMPORTANT]  
> 由于 local.settings.json 文件包含机密，因此永远不会发布它，应将其从源代码管理中排除。

需要 `AzureWebJobsStorage` 值，即存储帐户连接字符串。 你将使用此连接来验证输出绑定是否按预期方式工作。
