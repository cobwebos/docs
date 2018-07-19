---
title: 使用 Visual Studio Code 开发 Java Azure Service Fabric 应用程序 | Microsoft Docs
description: 本文介绍如何使用 Visual Studio Code 生成、部署和调试 Java Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 54c94c50f6292694e947d97a10fd6976c14e19df
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115276"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>使用 Visual Studio Code 开发 Java Service Fabric 应用程序

使用[适用于 VS Code 的 Service Fabric Reliable Services 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)可在 Windows、Linux 和 macOS 操作系统上轻松生成 Java Service Fabric 应用程序。

本文介绍如何使用 Visual Studio Code 生成、部署和调试 Java Service Fabric 应用程序。

> [!IMPORTANT]
> 可在 Windows 计算机上开发 Service Fabric Java 应用程序，但只能将其部署到 Azure Linux 群集。 Windows 不支持调试 Java 应用程序。

## <a name="prerequisites"></a>先决条件

本文假设你已安装 VS Code、适用于 VS Code 的 Service Fabric Reliable Services 扩展，以及开发环境所需的所有依赖项。 有关详细信息，请参阅[入门](./service-fabric-get-started-vs-code.md#prerequisites)。

## <a name="download-the-sample"></a>下载示例
本文使用 [Service Fabric Java 应用程序快速入门示例 GitHub 存储库](https://github.com/Azure-Samples/service-fabric-java-quickstart)中的投票应用程序。 

若要将此存储库克隆到开发计算机，请在终端窗口（Windows 上的命令窗口）中运行以下命令：

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>在 VS Code 中打开应用程序

打开 VS Code。  单击**活动栏**中的“Explorer”图标并单击“打开文件夹”，或者单击“文件”->“打开文件夹”。 导航到存储库克隆到的文件夹中的 *./service-fabric-java-quickstart/Voting* 目录，然后单击“确定”。 工作区应包含以下屏幕截图中所示的相同文件。

![工作区中的 Java 投票应用程序](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>构建应用程序

1. 在 VS Code 中，按 (Ctrl + Shift + p) 打开**命令面板**。
2. 搜索并选择“Service Fabric: 生成应用程序”命令。 生成输出将发送到集成式终端。

   ![VS Code 中的“生成应用程序”命令](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>将应用程序部署到本地群集
生成应用程序后，可将其部署到本地群集。 

> [!IMPORTANT]
> Windows 计算机上不支持将 Java 应用程序部署到本地群集。

1. 在**命令面板**中，选择“Service Fabric: 部署应用程序(Localhost)”命令。 安装过程的输出将发送到集成式终端。

   ![VS Code 中的“部署应用程序”命令](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. 部署完成后，启动浏览器并打开 Service Fabric Explorer： http://localhost:19080/Explorer 。 应会看到应用程序正在运行。 此过程可能需要一段时间，请耐心等待。 

   ![Service Fabric Explorer 中的投票应用程序](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. 确认应用程序正在运行后，启动浏览器并打开此页： http://localhost:8080 。 这是应用程序的 Web 前端。 可以添加项，并单击这些项进行投票。

   ![浏览器中的投票应用程序](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. 若要从群集中删除应用程序，请在**命令面板**中选择“Service Fabric: 删除应用程序”命令。 卸载过程的输出将发送到集成式终端。 可以使用 Service Fabric Explorer 来验证是否已从本地群集中删除该应用程序。

## <a name="debug-the-application"></a>调试应用程序
在 VS Code 中调试应用程序时，该应用程序必须在本地群集上运行。 然后，可将断点添加到代码中。

> [!IMPORTANT]
> Windows 计算机上不支持调试 Java 应用程序。

若要准备好调试 VotingDataService 和投票应用程序，请完成以下步骤：

1. 更新 *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* 文件。
注释掉第 6 行中的命令（使用 #），并将以下命令添加到文件的底部：

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. 更新 *Voting/VotingApplication/ApplicationManifest.xml* 文件。 在 **StatefulService** 元素中将 **MinReplicaSetSize** 和 **TargetReplicaSetSize** 属性设置为“1”：
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. 单击**活动栏**中的“调试”图标，在 VS Code 中打开调试程序视图。 单击调试程序视图顶部的齿轮图标，从环境下拉菜单中选择“Java”。 此时会打开 launch.json 文件。 

   ![VS Code 工作区中的调试图标](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. 在 launch.json 文件中，将名为 **Debug (Attach)** 的配置中的端口值设置为 **8001**。 保存文件。

   ![调试 launch.json 的配置](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. 使用“Service Fabric: 部署应用程序(Localhost)”命令将应用程序部署到本地群集。 验证该应用程序是否在 Service Fabric Explorer 中运行。 现在，可以开始调试应用程序。

若要设置断点，请完成以下步骤：

1. 在 Explorer 中，打开 */Voting/VotingDataService/src/statefulservice/VotingDataService.java* 文件。 在 `addItem` 方法中 `try` 块内的第一个代码行（第 80 行）上设置一个断点。
   
   ![在投票数据服务中设置断点](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > 确保在可执行的代码行上设置断点。 例如，调试程序会遗漏方法声明、`try` 语句或 `catch` 语句上设置的断点。
2. 若要开始调试，请单击**活动栏**中的“调试”图标，从调试菜单中选择“Debug (Attach)”配置，然后单击运行按钮（绿色箭头）。

   ![Debug (Attach) 配置](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. 在 Web 浏览器中转到 http://localhost:8080。 在文本框中键入新项，然后单击“+ 添加”。 应会命中该断点。 可以使用 VS Code 顶部的“调试”工具栏继续执行、逐行执行每行、逐步执行方法，或跳出当前方法。 
   
   ![命中断点](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. 若要结束调试会话，请单击 VS Code 顶部的“调试”工具栏上的插入图标。
   
   ![从调试程序断开连接](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. 完成调试后，可以使用“Service Fabric: 删除应用程序”命令以从本地群集中删除投票应用程序。 

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 VS Code 开发和调试 C# Service Fabric 应用程序](./service-fabric-develop-csharp-applications-with-vs-code.md)。
