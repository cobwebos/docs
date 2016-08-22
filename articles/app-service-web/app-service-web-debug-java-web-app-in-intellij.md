<!-- not suitable for Mooncake -->


<properties 
	pageTitle="在 IntelliJ 中调试 Azure 上的 Java Web 应用 | Azure" 
	description="本教程演示如何使用 Azure Toolkit for IntelliJ 调试在 Azure 上运行的 Java Web 应用。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="06/27/2016"
	wacn.date=""/>

# 在 IntelliJ 中调试 Azure 上的 Java Web 应用

本教程演示如何使用 [Azure Toolkit for IntelliJ] 调试在 Azure 上运行的 Java Web 应用。为简单起见，本教程将使用基本的 Java Server Page (JSP) 示例，但是在 Azure 中进行调试时对于 Java servlet 步骤是类似的。

完成本教程后，在 IntelliJ 中调试你的应用程序时，该应用程序如下图所示：

![][01]  

 
## 先决条件

* Java 开发人员工具包 (JDK) 1.8 或更高版本。
* IntelliJ IDEA 旗舰版。可以从 <https://www.jetbrains.com/idea/download/index.html> 下载。
* 分发基于 Java 的 Web 服务器或应用程序服务器，例如 Apache Tomcat 或 Jetty。
* Azure 订阅，可以从 <https://azure.microsoft.com/free/> 或 </pricing/overview/> 获取。
* Azure Toolkit for IntelliJ。有关详细信息，请参阅[安装 Azure Toolkit for IntelliJ]。
* 已创建动态 Web 项目并将其部署到 Azure Web 应用；有关示例请参阅 [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]。

## 调试 Azure 上的 Java Web 应用

若要完成本节中的这些步骤，可以使用现有的已在 Azure 中部署为 Java Web 应用的动态 Web 项目，或者你可以下载[示例动态 Web 项目]，然后按照[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]中的步骤在 Azure 中部署该项目。

1. 打开在 IntelliJ 中部署到 Azure 的 Java Web 应用项目。

1. 单击“运行”菜单，然后单击“编辑配置...”。

    ![][02]  


1. 当“运行/调试配置”对话框时，请执行以下操作：

    1. 选择“App Service”。
    1. 单击“+”以添加新配置。
    1. 提供配置的“名称”。
    1. 接受（由 Azure Toolkit 建议的）其余默认值，然后单击“确定”。

        ![][03]

1. 在菜单右上角选择刚才创建的 Azure Web 应用调试配置，然后单击“调试”

    ![][04]  


1. 当出现“是否立即启用远程 Web 应用中的远程调试?”提示时，单击“确定”。

1. 当系统提示“你的 Web 应用现在可以进行远程调试了”，请单击“确定”。

    ![][05]  


1. 在菜单右上角选择刚才创建的 Azure Web 应用调试配置，然后单击“调试”。

1. 将打开 Windows 命令提示符或 Unix shell，并准备调试所需的连接；需等到远程 Java Web 应用连接成功才能继续操作。如果你使用 Windows，将如下图所示：

    ![][06]  


1. 将断点插入 JSP 页面，然后在浏览器中打开 Java Web 应用的 URL：

    1. 在 IntelliJ 中打开“Azure 资源管理器”。
    1. 导航到“Web Apps”以及要调试的 Java Web 应用。
    1. 右键单击 Web 应用，然后单击“在浏览器中打开”。
    1. IntelliJ 现在将进入调试模式。

## 后续步骤

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]。

有关创建 Azure Web Apps 的其他信息，请参阅 [Web Apps 概述]。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->


[Azure Web App]: /documentation/services/web-sites/
[Azure Toolkit for IntelliJ]: /documentation/articles/azure-toolkit-for-intellij/
[安装 Azure Toolkit for IntelliJ]: /documentation/articles/azure-toolkit-for-intellij-installation/
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: /documentation/articles/app-service-web-intellij-create-hello-world-web-app/
[示例动态 Web 项目]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java 开发人员中心]: /develop/java/
[Web Apps 概述]: /home/features/web-site/

<!-- IMG List -->


[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=Mooncake_0815_2016-->