---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132262"
---
## <a name="deploy-the-function-app-project-to-azure"></a>将函数应用项目部署到 Azure

在 Azure 中创建函数应用后，可以使用 [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools 命令将项目代码部署到 Azure。 在下面的命令中，将 `<APP_NAME>` 替换为上一步中你的应用的名称。

```bash
func azure functionapp publish <APP_NAME>
```

你将看到类似于以下内容的输出，为了提高可读性，已经截断了这些输出。

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

复制你的 HttpTrigger 的调用 URL 值，现在可以使用它在 Azure 中测试你的函数。 该 URL 包含一个 `code` 查询字符串值，该值是你的函数密钥。 此密钥使得其他人难以在 Azure 中调用你的 HTTP 触发器终结点。