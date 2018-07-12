---
title: 调试本地 Service Fabric 群集上的 Java 应用 |Microsoft Docs
description: 本教程介绍如何调试本地群集上运行的 Service Fabric Java 应用程序并获取其日志。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: c1a8b18062f61be9eb020beefd3ad741c41b55f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652696"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>教程：调试本地 Service Fabric 群集上部署的 Java 应用程序

本教程是一个系列中的第二部分。 其中介绍了如何使用适用于 Service Fabric 应用程序的 Eclipse 来附加远程调试器。 此外，介绍如何将运行中应用程序发出的日志重定向到方便开发人员处理的位置。

本系列教程的第二部分将介绍如何：
> [!div class="checklist"]
> * 使用 Eclipse 调试 Java 应用程序
> * 将日志重定向到可配置的位置

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [生成 Java Service Fabric Reliable Services 应用程序](service-fabric-tutorial-create-java-app.md)
> * 在本地群集上部署和调试应用程序
> * [将应用程序部署到 Azure 群集](service-fabric-tutorial-java-deploy-azure.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-java-elk.md)
> * [设置 CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 设置适用于 [Mac](service-fabric-get-started-mac.md) 或 [Linux](service-fabric-get-started-linux.md) 的开发环境。 遵照说明安装 Eclipse 插件、Gradle、Service Fabric SDK 和 Service Fabric CLI (sfctl)。

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序

如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-java-app.md)中的投票示例应用程序，还可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[生成应用程序并将其部署](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster)到本地开发群集。

## <a name="debug-java-application-using-eclipse"></a>使用 Eclipse 调试 Java 应用程序

1. 在计算机上打开 Eclipse IDE，单击“文件”->“导入...”。

2. 在弹出窗口中，选择“常规”->“将现有项目导入工作区”选项并按“下一步”。

3. 在“导入项目”窗口中，选择“选择根目录”选项并选取“Voting”目录。 如果已学完了系列教程 1，则“Voting”目录出现在“Eclipse-workspace”目录中。

4. 更新要调试的服务的 entryPoint.sh，以便使用远程调试参数启动 Java 进程。 本教程使用了无状态前端：*Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*。在此示例中调试时，设置端口 8001。

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. 通过将要调试的服务的实例计数或副本计数设置为 1 来更新应用程序清单。 此设置可避免用于调试的端口出现冲突。 例如，对于无状态服务，设置 ``InstanceCount="1"``；对于有状态服务，将目标和最小副本集大小设置为 1，如下所示：``TargetReplicaSetSize="1" MinReplicaSetSize="1"``。

6. 在 Eclipse IDE 中，选择“运行”->“调试配置”->“远程 Java 应用程序”，按“新建”按钮，设置以下属性，然后单击“应用”。

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. 在 *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* 文件的第 109 行中插入一个断点。

8. 在包资源管理器中右键单击“投票”项目，然后单击“Service Fabric”->“发布应用程序...”。

9. 在“发布应用程序”窗口上的下拉列表中，选择“Local.json”并单击“发布”。

10. 在 Eclipse IDE 中，选择“运行”->“调试配置”->“远程 Java 应用程序”，单击创建的“投票”配置，然后单击“调试”。

11. 转到 Web 浏览器并访问 **localhost:8080** 以命中断点，然后在 Eclipse 中输入“调试透视图”。

## <a name="redirect-application-logs-to-custom-location"></a>将应用程序日志重定向到自定义位置

以下步骤说明如何将应用程序日志从默认的 */var/log/syslog* 位置重定向到自定义位置。

1. 目前，Service Fabric Linux 群集中运行的应用程序仅支持选取一个日志文件。 因此，日志始终会发到 */tmp/mysfapp0.0.log*。 在 *Voting/VotingApplication/VotingWebPkg/Code/logging.properties* 位置创建名为 logging.properties 的文件，并添加以下内容。

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location. You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. 在 *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* 中添加 Java 执行命令的以下参数：

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    以下示例演示了示例执行：

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

现在，你已了解如何在开发 Service Fabric Java 应用程序时调试和访问应用程序日志。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 使用 Eclipse 调试 Java 应用程序
> * 将日志重定向到可配置的位置

转到下一教程：
> [!div class="nextstepaction"]
> [将应用程序部署到 Azure](service-fabric-tutorial-java-deploy-azure.md)