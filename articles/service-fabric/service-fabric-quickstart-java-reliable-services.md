---
title: 在 Azure 中的 Service Fabric 上创建 Java 应用 | Microsoft Docs
description: 在本快速入门中，请使用 Service Fabric Reliable Services 示例应用程序创建用于 Azure 的 Java 应用程序。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: ddd56b8479678b288424dd896baadea6a41a2aef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008141"
---
# <a name="quickstart-deploy-a-java-reliable-services-application-to-service-fabric"></a>快速入门：将 Java Reliable Services 应用程序部署到 Service Fabric

Azure Service Fabric 是一款分布式系统平台，可用于部署和管理微服务和容器。

此快速入门演示如何在 Linux 开发人员计算机上使用 Eclipse IDE 将首个 Java 应用程序部署到 Service Fabric。 完成后，将生成一个带 Java Web 前端的投票应用程序，用于将投票结果保存到群集的有状态后端服务中。

![应用程序屏幕截图](./media/service-fabric-quickstart-java/votingapp.png)

此快速入门介绍如何：

* 使用 Eclipse 处理 Service Fabric Java 应用程序
* 将应用程序部署到本地群集
* 跨多个节点横向扩展应用程序

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

1. [安装 Service Fabric SDK 和 Service Fabric 命令行接口 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [安装 Git](https://git-scm.com/)
3. [安装 Eclipse](https://www.eclipse.org/downloads/)
4. [设置 Java 环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)，确保遵循可选步骤安装 Eclipse 插件

## <a name="download-the-sample"></a>下载示例

在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>在本地运行应用程序

1. 通过运行以下命令来启动本地群集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    启动本地群集需要一些时间。 若要确认群集是否完全正常，请访问 Service Fabric Explorer（网址：**http://localhost:19080**）。 5 个节点均正常即表示本地群集运行正常。

    ![本地群集正常运行](./media/service-fabric-quickstart-java/localclusterup.png)

2. 打开 Eclipse。
3. 单击“文件”- >“导入”- > Gradle - > 现有 Gradle 项目，然后按照向导进行操作。
4. 单击“目录”，然后在从 GitHub 克隆的 `service-fabric-java-quickstart` 文件夹中选择 `Voting` 目录。 单击“完成”。

    ![Eclipse 的“导入”对话框](./media/service-fabric-quickstart-java/eclipseimport.png)

5. Eclipse 的包资源管理器中现拥有 `Voting` 项目。
6. 右键单击该项目并选择“Service Fabric”下拉列表中的“发布应用程序...”。 选择“PublishProfiles/Local.json”为目标配置文件，然后单击“发布”。

    ![本地“发布”对话框](./media/service-fabric-quickstart-java/localjson.png)

7. 打开喜欢的 Web 浏览器并访问应用程序（网址：`http://localhost:8080`）。

    ![本地应用程序前端](./media/service-fabric-quickstart-java/runninglocally.png)

现在可以添加一组投票选项，并开始进行投票。 此应用程序可以运行，并将所有数据存储到 Service Fabric 群集中，而无需单独提供数据库。

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务

可跨群集缩放服务来适应服务负载的变化。 可以通过更改群集中运行的实例数量来缩放服务。 存在多种服务缩放方式，例如，可使用 Service Fabric CLI (sfctl) 脚本/命令。 以下步骤使用 Service Fabric Explorer。

Service Fabric Explorer 在所有 Service Fabric 群集中运行，并能通过浏览器进行访问，访问方法是转到群集的 HTTP 管理端口 (19080)，例如，`http://localhost:19080`。

若要缩放 Web 前端服务，请执行以下操作：

1. 在群集中打开 Service Fabric Explorer - 例如 `https://localhost:19080`。
2. 单击树视图中 fabric:/Voting/VotingWeb 节点旁边的省略号（三个点），再选择“缩放服务”。

    ![Service Fabric Explorer 缩放服务](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    现在可以缩放 Web 前端服务的实例数量。

3. 将数字更改为 2，再单击“缩放服务”。
4. 单击树视图中的 fabric:/Voting/VotingWeb 节点，再展开分区节点（由 GUID 表示）。

    ![Service Fabric Explorer 缩放服务完成](./media/service-fabric-quickstart-java/servicescaled.png)

    现在可以看到，服务有两个实例。在树视图中可以查看实例的运行节点。

通过这一简单的管理任务，你已让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需多个实例便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 使用 Eclipse 处理 Service Fabric Java 应用程序
* 将 Java 应用程序部署到本地群集
* 跨多个节点横向扩展应用程序

若要详细了解如何在 Service Fabric 中使用 Java 应用，请继续学习适用于 Java 应用的教程。

> [!div class="nextstepaction"]
> [部署 Java 应用](./service-fabric-tutorial-create-java-app.md)
