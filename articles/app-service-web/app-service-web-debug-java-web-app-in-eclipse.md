<!-- not suitable for Mooncake -->


<properties 
	pageTitle="在 Eclipse 中调试 Azure 的 Java Web 应用 | Azure" 
	description="本教程演示如何使用用于 Eclipse 的 Azure 工具包调试在 Azure 上运行的 Java Web 应用。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="06/27/2016"
	wacn.date=""/>  


# 在 Eclipse 中调试 Azure 的 Java Web 应用

本教程演示如何使用[用于 Eclipse 的 Azure 工具包]调试在 Azure 上运行的 Java Web 应用。为简单起见，本教程将使用基本的 Java Server Page (JSP) 示例，但是在 Azure 中进行调试时对于 Java servlet 步骤是类似的。

完成本教程后，在 Eclipse 中调试你的应用程序时，该应用程序如下图所示：

![][01]  

 
## 先决条件

* Java 开发人员工具包 (JDK) 1.8 或更高版本。
* Eclipse IDE for Java EE Developers, Indigo 或更高版本。可以从 <http://www.eclipse.org/downloads/> 下载。
* 分发基于 Java 的 Web 服务器或应用程序服务器，例如 Apache Tomcat 或 Jetty。
* Azure 订阅，可以从 <https://azure.microsoft.com/free/> 或 </pricing/overview/> 获取。
* Azure Toolkit for Eclipse。有关详细信息，请参阅[安装 Azure Toolkit for Eclipse]。
* 已创建并部署到 Azure Web 应用的动态 Web 项目；例如，请参阅[在 Eclipse 中创建用于 Azure 的 Hello World Web 应用]。

## 调试 Azure 上的 Java Web 应用

若要完成本节中的这些步骤，可以使用现有的已在 Azure 中部署为 Java Web 应用的动态 Web 项目，或者你可以下载[示例动态 Web 项目]，然后按照[在 Eclipse 中创建用于 Azure 的 Hello World Web 应用]中的步骤在 Azure 中部署该项目。

1. 打开 Eclipse。

1. 配置用于远程调试的超时值：

    1. 单击 Eclipse 中的“Windows”菜单，然后单击“首选项”。
    1. 展开“Java”节点，然后选择“调试”。
    1. 将“调试器超时 (ms)”和“启动超时 (ms)”设置均配置为 `120000`。

        ![][02]  


    1. 单击“确定”以关闭“首选项”对话框。

1. 在 Eclipse 的项目资源管理器视图中，右键单击已部署到 Azure 的动态 Web 项目。显示上下文菜单时，选择“调试方式”，然后单击“应用程序服务”。

    ![][03]  


1. 如果是首次调试动态 Web 项目，将打开“调试配置”对话框；你可以接受“连接”选项卡上由工具包指定的默认值。在“源”选项卡中，依次单击“添加”、“Java 项目”，选择“动态 Web 项目”，然后单击“确定”。完成这些步骤后，单击“调试”。

    ![][04]  


1. 当出现“是否立即启用远程 Web 应用中的远程调试?”提示时，单击“确定”。

1. 当系统提示“你的 Web 应用现在可以进行远程调试了”，请单击“确定”。

    ![][05]  


1. 再次出现“调试配置”对话框时，请单击“调试”。

1. 将打开 Windows 命令提示符或 Unix shell，并准备调试所需的连接；需等到远程 Java Web 应用连接成功才能继续操作。如果使用的是 Windows，则如下图所示。

    ![][06]  


1. 将断点插入 JSP 页面，然后在浏览器中打开 Java Web 应用的 URL：

    1. 在 Eclipse 中打开 **Azure 资源管理器**。
    1. 导航到“Web Apps”以及要调试的 Java Web 应用。
    1. 右键单击 Web 应用，然后单击“在浏览器中打开”。
    1. Eclipse 现在将进入调试模式。

## 后续步骤

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]。

有关创建 Azure Web Apps 的其他信息，请参阅 [Web Apps 概述]。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->


[Azure Web App]: /documentation/services/web-sites/
[用于 Eclipse 的 Azure 工具包]: /documentation/articles/azure-toolkit-for-eclipse/
[安装 Azure Toolkit for Eclipse]: /documentation/articles/azure-toolkit-for-eclipse-installation/
[在 Eclipse 中创建用于 Azure 的 Hello World Web 应用]: /documentation/articles/app-service-web-eclipse-create-hello-world-web-app/
[示例动态 Web 项目]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java 开发人员中心]: /develop/java/
[Web Apps 概述]: /home/features/web-site/

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=Mooncake_0815_2016-->