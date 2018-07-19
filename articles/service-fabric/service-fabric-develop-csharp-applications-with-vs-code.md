---
title: 使用 Visual Studio Code 开发 .NET Core Azure Service Fabric 应用程序 | Microsoft Docs
description: 本文介绍如何使用 Visual Studio Code 生成、部署和调试 .NET Core Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 27c7c62125f3f559fb1764292729cbbfdc1c4e5f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115313"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>使用 Visual Studio Code 开发 C# Service Fabric 应用程序

使用[适用于 VS Code 的 Service Fabric Reliable Services 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)可在 Windows、Linux 和 macOS 操作系统上轻松生成 .NET Core Service Fabric 应用程序。

本文介绍如何使用 Visual Studio Code 生成、部署和调试 .NET Core Service Fabric 应用程序。

## <a name="prerequisites"></a>先决条件

本文假设你已安装 VS Code、适用于 VS Code 的 Service Fabric Reliable Services 扩展，以及开发环境所需的所有依赖项。 有关详细信息，请参阅[入门](./service-fabric-get-started-vs-code.md#prerequisites)。

## <a name="download-the-sample"></a>下载示例
本文使用 [Service Fabric .NET Core 入门示例 GitHub 存储库](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)中的 CounterService 应用程序。 

若要将此存储库克隆到开发计算机，请在终端窗口（Windows 上的命令窗口）中运行以下命令：

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>在 VS Code 中打开应用程序

### <a name="windows"></a>Windows
右键单击“开始”菜单中的 VS Code 图标，并选择“以管理员身份运行”。 若要将调试程序附加到服务，需要以管理员身份运行 VS Code。

### <a name="linux"></a>Linux
使用终端导航到应用程序本地克隆到的目录中的 /service-fabric-dotnet-core-getting-started/Services/CounterService 路径。

运行以下命令，以 root 用户身份打开 VS Code，以便将调试程序附加到服务。
```
sudo code . --user-data-dir='.'
```

现在，该应用程序应会显示在 VS Code 工作区中。

![工作区中的计数器服务应用程序](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>构建应用程序
1. 在 VS Code 中，按 (Ctrl + Shift + p) 打开**命令面板**。
2. 搜索并选择“Service Fabric: 生成应用程序”命令。 生成输出将发送到集成式终端。

   ![VS Code 中的“生成应用程序”命令](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>将应用程序部署到本地群集
生成应用程序后，可将其部署到本地群集。 

1. 在**命令面板**中，选择“Service Fabric: 部署应用程序(Localhost)”命令。 安装过程的输出将发送到集成式终端。

   ![VS Code 中的“部署应用程序”命令](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. 部署完成后，启动浏览器并打开 Service Fabric Explorer： http://localhost:19080/Explorer 。 应会看到应用程序正在运行。 此过程可能需要一段时间，请耐心等待。 

   ![Service Fabric Explorer 中的计数器服务应用程序](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. 确认应用程序正在运行后，启动浏览器并打开此页： http://localhost:31002 。 这是应用程序的 Web 前端。 刷新页面，查看递增的当前计数器值。

   ![浏览器中的计数器服务应用程序](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>调试应用程序
在 VS Code 中调试应用程序时，该应用程序必须在本地群集上运行。 然后，可将断点添加到代码中。

若要设置断点并调试，请完成以下步骤：
1. 在 Explorer 中打开 */src/CounterServiceApplication/CounterService/CounterService.cs* 文件，并在 `RunAsync` 方法中的第 62 行处设置一个断点。
3. 单击**活动栏**中的“调试”图标，在 VS Code 中打开调试程序视图。 单击调试程序视图顶部的齿轮图标，从环境下拉菜单中选择“.NET Core”。 此时会打开 launch.json 文件。 可以关闭此文件。 现在，运行按钮（绿色箭头）旁边的调试配置菜单中应会出现配置选项。

   ![VS Code 工作区中的调试图标](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. 在调试配置菜单中选择“.NET Core 附加”。

   ![VS Code 工作区中的调试图标](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. 在浏览器中打开 Service Fabric Explorer： http://localhost:19080/Explorer 。 单击“应用程序”并向下钻取，以确定运行 CounterService 的主节点。 在下图中，CounterService 的主节点是“节点 0”。

   ![CounterService 的主节点](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. 在 VS Code 中，单击“.NET Core 附加”调试配置旁边的运行图标（绿色箭头）。 在进程选择对话框中，选择在步骤 4 中识别的主节点上运行的 CounterService 进程。

   ![主进程](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. 很快就会命中 *CounterService.cs* 文件中的断点。 然后可以浏览本地变量的值。 使用 VS Code 顶部的“调试”工具栏继续执行、逐行执行每行、逐步执行方法，或跳出当前方法。 

   ![调试值](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. 若要结束调试会话，请单击 VS Code 顶部的“调试”工具栏上的插入图标。
   
   ![从调试程序断开连接](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. 完成调试后，可以使用“Service Fabric: 删除应用程序”命令以从本地群集中删除 CounterService 应用程序。 

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 VS Code 开发和调试 Java Service Fabric 应用程序](./service-fabric-develop-java-applications-with-vs-code.md)。



