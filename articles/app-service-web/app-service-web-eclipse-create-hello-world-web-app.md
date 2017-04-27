---
title: "使用 Eclipse 创建基本的 Azure Web 应用 | Microsoft Docs"
description: "本教程说明如何使用 Azure Toolkit for Eclipse 创建适用于 Azure 的 Hello World Web 应用。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 9bb0b5fa32cd6dabd6c41106db38a587b0a55eed
ms.openlocfilehash: 18299966173f030f615049eaf11a55a1f71305a0
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-basic-azure-web-app-using-eclipse"></a>使用 Eclipse 创建基本的 Azure Web 应用
本教程说明如何使用[用于 Eclipse 的 Azure 工具包]创建一个基本的 Hello World 应用程序，并将其部署到 Azure 作为 Web 应用。 为简单起见显示了一个基本 JSP 示例，但就 Azure 部署而言，相似的步骤也适用于 Java servlet。

完成本教程后，你的应用程序将在 Web 浏览器中如下图所示：

![Hello World 应用预览][01]

## <a name="prerequisites"></a>先决条件
* Java 开发人员工具包 (JDK) 1.8 或更高版本。
* Eclipse IDE for Java EE Developers，Luna 或更高版本。 可从 <http://www.eclipse.org/downloads/> 下载。
* 分发基于 Java 的 Web 服务器或应用程序服务器，例如 [Apache Tomcat] 或 [Jetty]。
* Azure 订阅，可从 <https://azure.microsoft.com/free/> 或 <http://azure.microsoft.com/pricing/purchase-options/> 获取。
* [用于 Eclipse 的 Azure 工具包]。 有关安装 Azure 工具包的详细信息，请参阅[安装用于 Eclipse 的 Azure 工具包]。

## <a name="to-create-a-hello-world-application"></a>创建 Hello World 应用程序
首先，我们将从创建 Java 项目开始。

1. 启动 Eclipse。在 Eclipse 中的菜单上，依次单击“文件”、新建和“动态 Web 项目”。 （如果在单击“文件”、“新建”后未看到“动态 Web 项目”作为可用项目列出，则执行以下操作：依次单击“文件”、“新建”、“项目...”，展开“Web”，单击“动态 Web 项目”，然后单击“下一步”。）
2. 在本教程中，项目命名为 **MyWebApp**。 你的屏幕应与下图中所示类似：
   
    ![创建新的动态 Web 项目][02]
3. 单击“完成” 。
4. 在 Eclipse 的项目资源管理器视图中，展开“MyWebApp”。 右键单击“WebContent”，单击“新建”，然后单击“JSP 文件”。
5. 在“新建 JSP 文件”对话框中，将文件命名为“index.jsp”，将父文件夹保留为“MyWebApp/WebContent”，然后单击“下一步”。
6. 基于本教程的目的，在“选择 JSP 模板”对话框中选择“新建 JSP 文件(html)”，然后单击“完成”。
7. 在 Eclipse 中打开 index.jsp 文件后，添加文本以将 **Hello World!** 动态显示 在现有 `<body>` 元素中。 更新后的 `<body>` 内容应类似于以下示例：
   
    `<body><b><% out.println("Hello World!"); %></b></body>` 
8. 保存 index.jsp。

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>将应用程序部署到 Azure Web 应用容器
有多种方式可以将 Java Web 应用程序部署到 Azure。 本教程说明其中一个最简单的方式：将应用程序部署到 Azure Web 应用容器，无需特殊的项目类型或额外的工具。 Azure 为提供 JDK 及 Web 容器软件，因此不需要自己上载；只需要 Java Web 应用。 这样，应用程序的发布过程只需数秒，连一分钟都不用。

1. 在 Eclipse 的项目资源管理器中，右键单击“MyWebApp”。
2. 在上下文菜单中选择“Azure”，然后单击“发布为 Azure Web 应用...”
   
    ![发布为 Azure Web 应用][03]
   
    另外，如果已在项目资源管理器中选择 Web 应用程序项目，则可单击工具栏中的“发布”下拉按钮，然后从该处选择“发布为 Azure Web 应用”：
   
    ![发布为 Azure Web 应用][14]
3. 如果你尚未从 Eclipse 登录到 Azure 中，系统会提示你登录 Azure 帐户：
   
    ![Azure 登录对话框][04]
   
    如果有多个 Azure 帐户，部分提示会在登录过程中显示多次，即使这些提示看起来是相同的。 发生此情况时，请遵循登录指示继续操作。
4. 在成功登录 Azure 帐户后，“管理订阅”对话框将显示与你的凭据关联的订阅列表。 如果列出了多个订阅，而你只想使用其中几个帐户，你可以选择取消选中要使用的订阅。 选择订阅后，单击“关闭”。
   
    ![管理订阅对话框][05]
5. 当“部署到 Azure Web 应用容器”对话框出现时，它将显示前面创建的所有 Web 应用容器；如果尚未创建任何容器，列表将是空白的。
   
    ![部署到 Azure Web 应用容器对话框][06]
6. 如果前面尚未创建 Azure Web 应用容器，或你想要将应用程序发布到新的容器，请使用以下步骤。 否则，请选择现有的 Web 应用容器，并跳到下面的步骤 7。
   
   1. 单击“新建...”
      
       ![部署到 Azure Web 应用容器对话框][15]
   2. 此时将显示“新建 Web 应用容器”对话框：
      
       ![新建 Web 应用容器对话框][07a]
   3. 为 Web 应用容器输入“DNS 标签”；这是在 Azure 中的 Web 应用程序构成主机 URL 的叶 DNS 标签。 （请注意该名称必须可用，且符合 Azure Web 应用命名要求。）
   4. 在“Web 容器”下拉菜单中，为应用程序选择适当的软件。
      
       当前，你可以从 Tomcat 8、Tomcat 7 或 Jetty 9 中选择。 Azure 将提供所选软件的最新分发版，并且该版本将基于由 JDK 8 创建并由 Azure 提供的 JDK 最新分发版运行。
   5. 在“订阅”下拉菜单中，选择要用于此部署的订阅。
   6. 在“资源组”下拉菜单中，选择要与 Web 应用关联的资源组。 （使用 Azure 资源组可以将相关资源组织在一起，以便于将它们一起删除。）
      
       可以选择现有资源组（如果有）并跳到下面的步骤 g，或者按照以下这些步骤创建新的资源组：
      
      * 单击“新建...”
      * 此时将显示“新建资源组”对话框：
        
          ![新建资源组对话框][08]
      * 在“名称”文本框中，为新的资源组指定名称。
      * 在“区域”下拉菜单中，为资源组选择适当的 Azure 数据中心位置。
      * 可选：默认情况下，Azure 自动将最新的 Java 8 分发版作为 JVM 部署到 Web 应用容器。 但是，可以根据 Web 应用的需要指定 JVM 的其他版本和分发版。 若要指定 Web 应用的 JDK，请单击“JDK”选项卡，然后选择下列选项之一：
        
        * **部署由 Azure Web 应用服务提供的默认 JDK**：此选项部署最新的 Java 8 分发版。
        * **部署 Azure 上可用的第三方 JDK**：使用此选项可从由 Microsoft Azure 提供的 JDK 列表中选择。
        * **从此下载位置部署我自己的 JDK**：此选项可以指定自己的 JDK 分发版，此 JDK 必须打包为 ZIP 文件并上载到公开可用的下载位置或你可以访问的 Azure 存储帐户。
          
          ![新建 Web 应用容器对话框][07b]
   7. 单击 **“确定”**。
   8. “应用服务计划”下拉菜单列出了与选定资源组关联的应用服务计划。 （应用服务计划指定了 Web 应用的位置、定价层以及计算实例大小等信息。 单个应用服务计划可用于多个 Web 应用，这也是从特定 Web 应用部署中单独维护它的原因。）
      
       可以选择现有 App Service 计划（如果有）并跳到下面的步骤 h，或者按照以下这些步骤创建新的 App Service 计划：
      
      * 单击“新建...”
      * 此时将显示“新建应用服务计划”对话框：
        
          ![新建应用服务计划对话框][09]
      * 在“名称”文本框中，为新的应用服务计划指定名称。
      * 在“位置”下拉菜单中，为计划选择适当的 Azure 数据中心位置。
      * 在“定价层”下拉菜单中，为计划选择适当的定价。 对于测试，可以选择“免费”。
      * 在“实例大小”下拉菜单中，为计划选择适当的实例大小。 对于测试，可以选择“小”。
   9. 完成所有上述步骤之后，“新建 Web 应用容器”对话框看起来应如下图所示：
      
       ![新建 Web 应用容器对话框][10]
   10. 单击“确定”完成创建新的 Web 应用容器。
       
        等待 Web 应用容器列表刷新，这需要几秒，然后，你新创建的 Web 应用容器应在列表中处于选中状态。
7. 现在你已准备好完成将 Web 应用部署到 Azure 的初始部署：
   
    ![部署到 Azure Web 应用容器对话框][11]
   
    单击“确定”将 Java 应用程序部署到选定的 Web 应用容器。
   
    默认情况下，应用程序部署为应用程序服务器的子目录。 如果想要将其部署为根应用程序，请选中“部署到根”复选框，然后单击“确定”。
8. 接下来，应会看到“Azure 活动日志”视图，其中指示了 Web 应用的部署状态。
   
    ![Azure 活动日志][12]
   
    将 Web 应用部署到 Azure 的过程只需几秒钟即可完成。 当应用程序准备就绪时，可在“状态”列中看到名为“已发布”的链接。 当你单击该链接时，将转到已部署的 Web 应用的主页。

## <a name="updating-your-web-app"></a>更新 Web 应用
更新现有运行中的 Azure Web 应用是一个快速而简单的过程，你可以使用两个更新选项：

* 可以更新现有 Java Web 应用的部署。
* 可以将其他 Java 应用程序发布到同一个 Web 应用容器。

对于任一情况，过程都是相同的，只需几秒钟即可完成：

1. 在 Eclipse 项目资源管理器中，右键单击要更新或添加到现有 Web 应用容器的 Java 应用程序。
2. 出现上下文菜单时，请选择“Azure”，然后单击“发布为 Azure Web 应用...”
3. 由于前面你已登录，因此将看到现有 Web 应用容器的列表。 选择要对其发布或重新发布 Java 应用程序的 Web 应用容器，然后单击“确定”。

几秒钟后，“Azure 活动日志”视图会将已更新的部署显示为“已发布”，可以在 Web 浏览器中验证已更新的应用程序。

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>启动、停止或重启现有的 Web 应用
若要启动或停止现有的 Azure Web 应用容器（包括其中所有已部署的 Java 应用程序），可以使用“Azure 资源管理器”视图。

如果“Azure 资源管理器”视图尚未打开，可以依次单击 Eclipse 中的“窗口”菜单、“显示视图”、“其他...”、“Azure”和“Azure 资源管理器”将它打开。 如果你事先未尚未登录，系统将提示你登录。

显示“Azure 资源管理器”视图后，使用以下步骤来启动或停止 Web 应用： 

1. 展开“Azure”节点。
2. 展开“Web 应用”节点。 
3. 右键单击所需的 Web 应用。
4. 出现上下文菜单时，单击“启动”、“停止”或“重启”。 请注意，菜单选项可识别上下文，因此，你只能停止正在运行的 Web 应用，或启动当前未运行的 Web 应用。
   
    ![停止现有的 Web 应用][13]

## <a name="next-steps"></a>后续步骤
有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [安装用于 Eclipse 的 Azure 工具包]
  * *在 Eclipse 中创建 Azure 的 Hello World Web 应用（本文）*
  * [用于 Eclipse 的 Azure 工具包的新增功能]
* [用于 IntelliJ 的 Azure 工具包]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]。

有关创建 Azure Web 应用的其他信息，请参阅 [Web 应用概述]。

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ../azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web-intellij-create-hello-world-web-app.md
[安装用于 Eclipse 的 Azure 工具包]: ../azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ../azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ../azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[Web 应用概述]: ./app-service-web-overview.md
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png

