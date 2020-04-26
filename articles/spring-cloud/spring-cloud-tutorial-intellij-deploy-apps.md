---
title: 教程 - 使用 IntelliJ 部署 Azure Spring Cloud 应用程序
description: 使用 IntelliJ 将应用程序部署到 Azure Spring Cloud。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731360"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>使用 IntelliJ 部署 Azure Spring Cloud 应用程序
适用于 Azure Spring Cloud 的 IntelliJ 插件支持从 IntelliJ IDEA 进行应用程序部署。  

## <a name="prerequisites"></a>先决条件
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA 社区版/旗舰版，版本 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>安装插件
可以从 IntelliJ 插件  UI 添加 Azure Toolkit for IntelliJ IDEA 3.35.0。

1. 启动 IntelliJ。  如果以前打开过某个项目，请关闭该项目以显示“欢迎”对话框。 从右下方的链接中选择“配置”，然后单击“插件”以打开插件配置对话框，然后选择“从磁盘安装插件”。   

    ![选择“配置”](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. 搜索“Azure Toolkit for IntelliJ”。  单击“安装”  。

    ![安装插件](media/spring-cloud-intellij-howto/install-plugin.png)

1. 单击“重启 IDE”  。

## <a name="tutorial-procedures"></a>教程过程
以下过程使用 IntelliJ IDEA 部署 Hello World 应用程序。

* 打开 gs-spring-boot 项目
* 部署到 Azure Spring Cloud
* 显示流式处理日志

## <a name="open-gs-spring-boot-project"></a>打开 gs-spring-boot 项目

1. 下载本教程的源存储库并将其解压缩，或使用 Git: git clone https://github.com/spring-guides/gs-spring-boot.git 克隆它 
1. 通过 cd 命令进入 gs-spring-boot\complete。
1. 打开 IntelliJ 的“欢迎”对话框，选择“导入项目”以打开导入向导   。
1. 选择 `gs-spring-boot\complete` 文件夹。

    ![导入项目](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>部署到 Azure Spring Cloud
若要部署到 Azure，必须使用你的 Azure 帐户登录，然后选择你的订阅。  有关登录的详细信息，请参阅[安装和登录](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)。

1. 在 IntelliJ 项目资源管理器中右键单击你的项目，选择“Azure” -> “部署到 Azure Spring Cloud”。  

    ![部署到 Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. 在“名称”  字段中接受应用的名称。  “名称”是指配置，不是应用名称。 用户通常不需更改它。
1. 接受来自项目的标识符作为“生成工件”。 
1. 选择“应用:”，然后单击“创建应用...”。  

    ![部署到 Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. 输入“应用名称”  ，然后单击“确定”  。

    ![部署到 Azure - 确定](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. 单击“运行”  按钮，开始部署。 

    ![部署到 Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. 此插件会对项目运行命令 `mvn package`，然后创建新的应用并部署由 `package` 命令生成的 jar。

1. 如果应用 URL 未显示在输出窗口中，请从 Azure 门户获取它。 从资源组导航到 Azure Spring Cloud 实例。  然后单击“应用”  。  将会列出正在运行的应用。

    ![获取测试 URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. 在浏览器中导航到该 URL。

    ![在浏览器中导航 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>显示流式处理日志
若要获取日志，请执行以下操作：
1. 选择“Azure 资源管理器”，然后选择“Spring Cloud”  。 
1. 右键单击正在运行的应用。
1. 从下拉列表中选择“流式处理日志”。 

    ![选择流式处理日志](media/spring-cloud-intellij-howto/streaming-logs.png)

1. 选择实例。

    ![选择实例](media/spring-cloud-intellij-howto/select-instance.png)

1. 流式处理日志将显示在输出窗口中。

    ![流式处理日志输出](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>后续步骤
* [准备适用于 Azure Spring Cloud 的 Spring 应用程序](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [详细了解 Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
