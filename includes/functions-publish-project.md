---
title: include 文件
description: include 文件
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452949"
---
## <a name="deploy-the-function-app-project-to-azure"></a>将函数应用项目部署到 Azure

在 Azure 中创建函数应用后，可以使用 [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) 命令将项目代码部署到 Azure。

```bash
func azure functionapp publish <FunctionAppName>
```

你将看到如下输出，为了可读性，这些输出已经被截断。

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

现在可以在 Azure 中测试函数。