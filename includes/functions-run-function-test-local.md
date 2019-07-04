---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172724"
---
## <a name="run-the-function-locally"></a>在本地运行函数

以下命令用于启动函数应用。 此应用使用 Azure 中的 Azure Functions 运行时来运行。

```bash
func host start --build
```

若要编译 C# 项目，`--build` 选项是必需的。 不需对 JavaScript 项目使用此选项。

Functions 主机启动时，会写入类似于以下输出的内容。为了可读性，这些输出已经被截断：

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

从运行时输出中复制 `HttpTrigger` 函数的 URL，将其粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。 下面演示浏览器中本地函数返回的对 GET 请求的响应：

![在浏览器中进行本地测试](./media/functions-run-function-test-local/functions-test-local-browser.png)

在本地运行函数以后，即可在 Azure 中创建函数应用和其他必需的资源。