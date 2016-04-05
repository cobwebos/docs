<!-- not suitable for Mooncake -->

<properties 
	pageTitle="在 Eclipse 中创建 Azure 的 Hello World Web 应用" 
	description="本教程说明如何使用 Azure Toolkit for Eclipse 创建适用于 Azure 的 Hello World Web 应用。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="03/07/2016"
	wacn.date=""/>

# 在 Eclipse 中创建 Azure 的 Hello World Web 应用

本教程说明如何使用 [Azure Toolkit for Eclipse] 创建一个基本的 Hello World 应用程序，并将其部署到 Azure 作为 Web 应用。所述的基本 JSP 示例是一个简化的示例，但就 Azure 部署而言，非常相似的步骤也适用于 Java servlet。

完成本教程后，你的应用程序将在 Web 浏览器中如下图所示：

![][01]
 
## 先决条件

* Java 开发人员工具包 (JDK) 1.7 或更高版本。
* Eclipse IDE for Java EE Developers, Indigo 或更高版本。可以从 <http://www.eclipse.org/downloads/> 下载。
* 分发基于 Java 的 Web 服务器或应用程序服务器，例如 Apache Tomcat 或 Jetty。
* Azure 订阅，可以从 <https://azure.microsoft.com/free/> 或 </pricing/overview/> 获取。
* Azure Toolkit for Eclipse。有关详细信息，请参阅[安装 Azure Toolkit for Eclipse]。

## 创建 Hello World 应用程序

首先，我们将从创建 Java 项目开始。

1. 启动 Eclipse。在 Eclipse 中的菜单上，依次单击“文件”、新建和“动态 Web 项目”。（如果在单击“文件”、“新建”后未看到“动态 Web 项目”作为可用项目列出，则执行以下操作：依次单击“文件”、“新建”、“项目...”，展开“Web”，单击“动态 Web 项目”，然后单击“下一步”。）
1. 在本教程中，项目命名为 MyHelloWorld。你的屏幕应与下图中所示类似：
   ![][02]
1. 单击“完成”。
1. 在 Eclipse 的项目资源管理器视图中，展开 MyHelloWorld。右键单击“WebContent”，单击“新建”，然后单击“JSP 文件”。
1. 在“新建 JSP 文件”对话框中，将文件命名为 index.jsp。将父文件夹保留为 MyHelloWorld/WebContent。
1. 对于本教程，请在“选择 JSP 模板”对话框中选择“新建 JSP 文件(html)”，然后单击“完成”。
1. 在 Eclipse 中打开 index.jsp 文件后，添加文本以便在现有 `<body>` 元素中动态显示 Hello World!。更新后的 `<body>` 内容应类似于以下示例：
   `<body><b><% out.println("Hello World!"); %></b></body>` 
1. 保存 index.jsp。

## 将应用程序部署到 Azure Web 应用容器

有多种方式可以将 Java Web 应用程序部署到 Azure。本教程说明其中一个最简单的方式：将应用程序部署到 Azure Web 应用容器，无需特殊的项目类型或额外的工具。Azure 为提供 JDK 及 Web 容器软件，因此不需要自己上载；只需要 Java Web 应用。这样，应用程序的发布过程只需数秒，连一分钟都不用。

1. 在 Eclipse 的项目资源管理器中，右键单击“MyHelloWorld”。

1. 在上下文菜单中选择“Azure”，然后单击“发布为 Azure Web 应用...”
   ![][03]
1. 如果你尚未从 Eclipse 登录 Azure，系统将提示登录 Azure 帐户：
   ![][04]
   注意：如果有多个 Azure 帐户，登录期间的某些提示可能显示多次，即使它们看起来似乎相同。发生此情况时，请遵循登录指示继续操作。
1. 在你成功登录 Azure 帐户后，“管理订阅”对话框将显示与你的凭据关联的订阅列表。如果列出了多个订阅，而你只想使用其中几个帐户，你可以选择取消选中要使用的订阅。选择订阅后，单击“关闭”。
   ![][05]
1. 当“部署到 Azure Web 应用容器”对话框出现时，它将显示前面创建的所有 Web 应用容器；如果尚未创建任何容器，列表将是空白的。   
   ![][06]
1. 如果前面尚未创建 Azure Web 应用容器，或你想要将应用程序发布到新的容器，请使用以下步骤。否则，请选择现有的 Web 应用容器，并跳到下面的步骤 7。

  1. 单击“新建...”

  1. 此时将显示“新建 Web 应用容器”对话框：

        ![][07]

  1. 为 Web 应用容器输入“DNS 标签”；这是在 Azure 中的 Web 应用程序构成主机 URL 的叶 DNS 标签。注意：该名称必须可用，且符合 Azure Web 应用命名要求。

  1. 在“Web 容器”下拉菜单中，为应用程序选择适当的软件。

        当前，你可以从 Tomcat 8、Tomcat 7 或 Jetty 9 中选择。Azure 将提供所选软件的最新分发版，并且该版本将基于由 JDK 8 创建并由 Azure 提供的 JDK 最新分发版运行。

  1. 在“订阅”下拉菜单中，选择要用于此部署的订阅。

  1. 在“资源组”下拉菜单中，选择要与 Web 应用关联的资源组。

        注意：使用 Azure 资源组可以将相关资源组织在一起，以便于执行删除等操作。

        可以选择现有资源组（如果有）并跳到下面的步骤 g，或者按照以下这些步骤创建新的资源组：

      * 单击“新建...”

      * 此时将显示“新建资源组”对话框：

            ![][08]

      * 在“名称”文本框中，为新的资源组指定名称。

      * 在“区域”下拉菜单中，为资源组选择适当的 Azure 数据中心位置。

      * 单击**“确定”**。

  1. “App Service 计划”下拉菜单列出了与选定资源组关联的 App Service 计划。

        注意：App Service 计划指定了你的 Web 应用的位置、定价层以及计算实例大小等信息。单个 App Service 计划可用于多个 Web 应用，这也是从特定 Web 应用部署单独维护它的原因。

        可以选择现有 App Service 计划（如果有）并跳到下面的步骤 h，或者按照以下这些步骤创建新的 App Service 计划：

      * 单击“新建...”

      * 此时将显示“新建 App Service 计划”对话框：

            ![][09]

      * 在“名称”文本框中，为新的 App Service 计划指定名称。

      * 在“位置”下拉菜单中，为计划选择适当的 Azure 数据中心位置。

      * 在“定价层”下拉菜单中，为计划选择适当的定价。对于测试，可以选择“免费”。

      * 在“实例大小”下拉菜单中，为计划选择适当的实例大小。对于测试，可以选择“小”。

  1. 完成所有上述步骤之后，“新建 Web 应用容器”对话框看起来应如下图所示：

        ![][10]

  1. 单击“确定”完成创建新的 Web 应用容器。

        等待 Web 应用容器列表刷新，这需要几秒，然后，你新创建的 Web 应用容器应在列表中处于选中状态。

1. 现在你已准备好完成将 Web 应用部署到 Azure 的初始部署：

    ![][11]

    单击“确定”将 Java 应用程序部署到选定的 Web 应用容器。

    注意：默认情况下，应用程序将部署为应用程序服务器的子目录。如果你想要部署为根应用程序，请选中“部署到根”复选框，然后单击“确定”。

1. 接下来，你应会看到“Azure 活动日志”视图，其中指示了 Web 应用的部署状态。

    ![][12]

    将 Web 应用部署到 Azure 的过程只需几秒钟即可完成。当应用程序准备就绪时，你会在“状态”列中看到名为“已发布”的链接。当你单击该链接时，将转到已部署的 Web 应用的主页。

## 更新 Web 应用

更新现有运行中的 Azure Web 应用是一个快速而简单的过程，你可以使用两个更新选项：

* 可以更新现有 Java Web 应用的部署。
* 可以将其他 Java 应用程序发布到同一个 Web 应用容器。

对于任一情况，过程都是相同的，只需几秒钟即可完成：

1. 在 Eclipse 项目资源管理器中，右键单击要更新或添加到现有 Web 应用容器的 Java 应用程序。

2. 出现上下文菜单时，请选择“Azure”，然后单击“发布为 Azure Web 应用...”

3. 由于前面你已登录，因此将看到现有 Web 应用容器的列表。选择要对其发布或重新发布 Java 应用程序的 Web 应用容器，然后单击“确定”。

几秒钟后，“Azure 活动日志”视图会将已更新的部署显示为“已发布”，你可以在 Web 浏览器中验证已更新的应用程序。

## 停止现有的 Web 应用

若要停止现有的 Azure Web 应用容器（包括其中所有已部署的 Java 应用程序），可以使用“Azure 资源管理器”视图。

如果“Azure 资源管理器”视图尚未打开，你可以依次单击 Eclipse 中的“Window”菜单、“显示视图”、“其他...”、“Azure”、“Azure 资源管理器”将它打开。如果你事先未尚未登录，系统将提示你登录。

显示“Azure 资源管理器”后，使用以下步骤来停止 Web 应用：

1. 展开“Azure”节点。
2. 展开“Web Apps”节点。 
3. 右键单击所需的 Web 应用。
5. 出现上下文菜单时，单击“停止”。
    ![][13]

## 后续步骤

有关详细信息，请参阅以下链接：

* [Java 开发人员中心](/develop/java/)。
* [Web Apps 概述](/home/features/web-site)

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Web App]: /documentation/services/web-sites/
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[安装 Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
<!---HONumber=Mooncake_0328_2016-->