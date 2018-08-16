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
ms.openlocfilehash: 8d0de9b027cff375526ef570e88540acfed3ef1c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129070"
---
### <a name="run-the-service"></a>运行该服务

1. 点击 F5（或在终端窗口键入 `azds up`）运行该服务。 该服务将自动在你新选择的空间 `default/scott` 中运行。 
1. 可以再次运行 `azds list-up` 来确认服务在其自己的空间中运行。 首先，你会注意到 `mywebapi` 的实例现在正在 `default/scott` 空间中运行（在 `default` 中运行的版本仍在运行，但未列出）。 其次，`webfrontend` 的接入点 URL 的前缀为文本“scott.s.”。 此 URL 对 `default/scott` 空间是唯一的。 此特殊 URL 表示发送到“scott URL”的请求会尝试先路由到 `default/scott` 空间中的服务，但如果失败，就会回退到 `default` 空间中的服务。

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

使用 Azure Dev Spaces 的这一内置功能，可以在共享空间内测试代码，无需每个开发人员都在自己的空间内重新创建完整的服务堆栈。 如本指南的上一步骤中所示，此路由需要应用代码转发传播标头。

### <a name="test-code-in-a-space"></a>在空间中测试代码
若要使用 `webfrontend` 测试新版本 `mywebapi`，请打开浏览器以转到 `webfrontend` 的公共接入点 URL，并转到“关于”页。 你应看到显示新消息。

现在，删除该 URL 的“scott.s” 部分，并刷新浏览器。 应该会看到旧行为（`mywebapi` 版本在 `default` 下运行）
