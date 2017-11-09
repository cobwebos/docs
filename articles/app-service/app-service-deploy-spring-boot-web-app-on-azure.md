---
title: "将 Spring Boot 应用程序部署到 Azure 应用服务 | Microsoft Docs"
description: "本教程会为开发者介绍将 Spring Boot 入门 Web 应用部署到 Azure 应用服务的步骤。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.openlocfilehash: 8776142d5452bf5057990702c89aa1a541382ffc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>将 Spring Boot 应用程序部署到 Azure 应用服务

[Spring Framework] 是一种开源解决方案，帮助 Java 开发者创建企业级应用程序，在该平台上构建的较常见的项目之一是 [Spring Boot]，它为创建独立的 Java 应用程序提供了一种简化的方法。

本教程将介绍如何创建 Spring Boot 入门 Web 应用示例，以及如何将其部署到 [Azure 应用服务]。

### <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下项：

* Azure 订阅；若尚未拥有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册获取[免费 Azure 帐户]。
* 最新的 [Java 开发人员工具包 (JDK)]。
* Apache 的 [Maven] 生成工具（版本 3）。
* [Git] 客户端。

## <a name="create-the-spring-boot-getting-started-web-app"></a>创建 Spring Boot 入门 Web 应用

以下步骤将引导你完成创建一个简单的 Spring Boot Web 应用程序和对其进行本地测试所需的步骤。

1. 打开命令提示符，创建本地目录以存放应用程序，并更改为以下目录；例如：
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - 或 -
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. 将 [Spring Boot 启动入门]示例项目克隆到刚刚创建的目录；例如：
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. 将目录更改为已完成项目；例如：
   ```
   cd gs-spring-boot
   cd complete
   ```

1. 使用 Maven 生成 JAR 文件；例如：
   ```
   mvn package
   ```

1. 创建 Web 应用后，将目录更改为 JAR 文件并启动 Web 应用；例如：
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. 使用 Web 浏览器浏览到 http://localhost:8080 以测试 Web 应用，如果有可用的 Curl，也可使用如以下示例所示的语法：
   ```
   curl http://localhost:8080
   ```

1. 应会显示以下消息：“来自 Spring Boot 的问候！”

   ![浏览示例应用][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>创建适用于 Java 的 Azure Web 应用

以下步骤将引导你完成创建 Azure Web 应用、配置在 Java 中所需的设置和配置 FTP 凭据的步骤。

1. 浏览到 [Azure 门户]并登录。

1. 登录 Azure 门户的帐户后，请单击“应用服务”的菜单图标：
   
   ![Azure 门户][AZ01]

1. 显示“应用服务”页面时，请单击“+ 添加”以创建新的应用服务。

   ![创建应用服务][AZ02]

1. 显示 Web 应用模板列表时，请单击基本 Microsoft Web 应用的链接。

   ![Web 应用模板][AZ03]

1. 显示 Web 应用模板的信息页面时，请单击“创建”。

   ![创建 Web 应用][AZ04]

1. 为 Web 应用提供唯一名称并指定任何其他设置，然后单击“创建”。

   ![创建 Web 应用设置][AZ05]

1. 创建 Web 应用后，单击“应用服务”的菜单图标，然后单击新创建的 Web 应用：

   ![列出 Web 应用][AZ06]

1. 显示 Web 应用时，请执行以下步骤以指定 Java 版本：

   a. 单击“应用程序设置”菜单项。

   b. Java 版本选择 "Java 8"。

   c. Java 次要版本选择“最新版”。

   d. Web 容器选择“最新的 Tomcat 8.5”。 （实际上不会使用此容器；Azure 会使用 Spring Boot 应用程序中的容器。）

   e. 单击“保存” 。

   ![应用程序设置][AZ07]

1. 通过执行以下步骤指定 FTP 部署凭据：

   a. 单击“部署凭据”菜单项。

   b. 指定用户名和密码。

   c. 单击“保存” 。

   ![指定部署凭据][AZ08]

1. 通过使用以下步骤检索 FTP 连接信息：

   a. 单击“部署凭据”菜单项。

   b. 复制完整的 FTP 用户名和 URL并保存，以供本教程下一节使用。

   ![FTP URL 和凭据][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>将 Spring Boot Web 应用部署到 Azure

以下步骤将引导你完成将 Spring Boot Web 应用部署到 Azure 的步骤。

1. 打开 Windows 记事本之类的文本编辑器，将以下文本粘贴到新文档中，然后将该文件另存为 "web.config"：
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. 将 "web.config" 文件保存到系统后，使用本教程前面部分保存的 URL、用户名和密码，通过 FTP 连接到 Web 应用。 例如：
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. 将远程目录更改为 Web 应用的根文件夹（在 /site/wwwroot 中），然后复制 Spring Boot 应用程序中的 JAR 文件和前面的 "web.config" 文件。 例如：
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. 将 JAR 和 "web.config" 文件部署到 Web 应用后，需要使用 Azure 门户重新启动 Web 应用：

   ![][AZ10]

1. 使用 Web 浏览器浏览到 Web 应用的 URL 以测试 Web 应用，如果有可用的 Curl，也可使用如以下示例所示的语法：
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. 应会显示以下消息：“来自 Spring Boot 的问候！”

   ![浏览示例应用][SB02]

## <a name="next-steps"></a>后续步骤

有关使用 Azure 上的 Spring Boot 应用程序的详细信息，请参阅以下文章：

* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Kubernetes 群集上](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关使用 FTP 将 Web 应用部署到 Azure 的其他信息，请参阅[使用 FTP/S 将应用部署到 Azure 应用服务]。

有关 Spring Boot 示例项目的详细信息，请参阅 [Spring Boot 启动入门]。

若要获取 Spring Boot 应用程序入门的相关帮助，请参阅 https://start.spring.io/ 中的“Spring Initializr”。

有关配置 Web 应用的其他设置的详细信息，请参阅[在 Azure 应用服务中配置 Web 应用]。

<!-- URL List -->

[Azure 应用服务]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[Azure 门户]: https://portal.azure.com/
[在 Azure 应用服务中配置 Web 应用]: /azure/app-service/web-sites-configure
[使用 FTP/S 将应用部署到 Azure 应用服务]: https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java 开发人员工具包 (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot 启动入门]: https://github.com/spring-guides/gs-spring-boot
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png
