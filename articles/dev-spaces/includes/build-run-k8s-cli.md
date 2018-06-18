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
ms.openlocfilehash: 484567dd9d9c3d050e7be25bd685a5b8d3de0687
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825494"
---
## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中生成并运行代码
让我们运行代码！ 在终端窗口中，从**根代码文件夹** webfrontend 运行以下命令：

```cmd
azds up
```

密切关注命令的输出，随着进展，你会注意到几件事情：
- 源代码已同步到 Azure 中的开发空间。
- 容器映像将在 Azure 中生成，由代码文件夹中的 Docker 资产指定。
- 将利用代码文件夹中 Helm 图表指定的容器映像创建 Kubernetes 对象。
- 将显示有关容器终结点的信息。 在我们的示例中，我们需要公共 HTTP URL。
- 假设上述阶段成功完成，你应该在容器启动时开始看到 `stdout`（和 `stderr`）输出。

> [!Note]
> 第一次运行 `up` 命令时，这些步骤需要较长时间，但后续运行应该会更快。

### <a name="test-the-web-app"></a>测试 Web 应用
扫描控制台输出以获取有关由 `up` 命令创建的公共 URL 的信息。 它的形式如下： 

`Running at public URL: http://<servicename>-<cluster-name>.<guid>.<region>.aksapp.io` 

在浏览器窗口中打开此 URL，你应看到 Web 应用加载。 在容器执行时，`stdout` 和 `stderr` 输出将流式传输到终端窗口。
