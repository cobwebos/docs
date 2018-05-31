---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367369"
---
### <a name="run-the-service"></a>运行该服务

1. 点击 F5（或在终端窗口键入 `azds up`）运行该服务。 该服务将自动在你新选择的空间 `scott` 中运行。 
1. 可以再次运行 `azds resource list` 来确认服务在其自己的空间中运行。 首先，你会注意到 `mywebapi` 的实例现在正在 `scott` 空间中运行（在 `default` 中运行的版本仍在运行，但未列出）。 其次，`webfrontend` 的接入点 URL 的前缀为文本 *scott.s.*。 此 URL 对 `scott` 空间是唯一的，并表示发送到“scott URL”的请求将尝试先路由到 `scott` 空间中的服务，然后回退到 `default` 空间中的服务。

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

使用 Azure Dev Spaces 的内置功能可在共享环境中对代码进行端到端测试，而无需每个开发人员在其空间中重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要应用代码转发传播标头。

## <a name="test-code-in-a-space"></a>在空间中测试代码
若要结合 `webfrontend` 测试 `mywebapi` 的新版本，请在浏览器中打开 webfrontend 的公共接入点 URL 并转到“关于”页。 你应看到显示新消息。

现在，删除该 URL 的“scott.s” 部分，并刷新浏览器。 你应该看到旧行为（由在 `default` 中运行的 `mywebapi` 版本展示）