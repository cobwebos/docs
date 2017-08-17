---
title: "使用适用于 IntelliJ 的 Azure 工具包将 Spring Boot 应用作为 Docker 容器发布 | Microsoft Docs"
description: "了解如何使用用于 IntelliJ 的 Azure 工具包将 Web 应用作为 Docker 容器发布到 Microsoft Azure。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 4f9c118fbfe445dd38670e3b7549a0d89681e5db
ms.contentlocale: zh-cn
ms.lasthandoff: 07/22/2017

---

# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij"></a>使用适用于 IntelliJ 的 Azure 工具包将 Spring Boot 应用作为 Docker 容器发布

[Spring Framework] 是一种开放源代码解决方案，可帮助 Java 开发人员创建企业级应用程序。 基于该平台构建的其中一个更常用的项目是 [Spring Boot]，该项目提供了简化的方法来创建独立的 Java 应用程序。

[Docker] 是一种开放源代码解决方案，可帮助开发人员自动部署、扩展和管理在容器中运行的应用程序。

本教程会指导你完成使用适用于 IntelliJ 的 Azure 工具包将 Spring Boot 应用程序作为 Docker 容器部署到 Microsoft Azure 的步骤。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repo"></a>克隆默认 Spring Boot Docker 存储库

以下步骤可指导你完成使用 IntelliJ 克隆 Spring Boot Docker 存储库的步骤。 如果要使用命令行，请参阅[在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上][Deploy Spring Boot on Linux in ACS]。

1. 打开 IntelliJ。

1. 在欢迎屏幕上，选择“从版本控制中签出”列表中的“GitHub”选项。

   ![用于版本控制的 GitHub 选项][CL01]

1. 如果系统提示登录，请输入凭据。

   * 如果使用用户名/密码登录到 GitHub：

      ![用于输入 GitHub 用户名和密码的对话框][CL02a]

   * 如果使用令牌登录到 GitHub：

      ![用于输入 GitHub 令牌的对话框][CL02b]

1. 针对存储库 URL 输入 https://github.com/spring-guides/gs-spring-boot-docker.git，指定本地路径和文件夹信息，然后单击“克隆”。

   ![“克隆存储库”对话框][CL03]

1. 当系统提示创建 IntelliJ 项目时，选择“否”。

   ![拒绝创建 IntelliJ 项目][CL04]

1. 在欢迎屏幕上，单击“导入项目”。

   ![导入项目选择][CL05]

1. 找到克隆了 Spring Boot 存储库的路径，选择根路径下的“complete”文件夹，然后单击“确定”。

   ![选择用于导入的文件夹][CL06]

1. 出现提示时，选择“从现有源创建项目”。

   ![从现有源创建项目的选项][CL07]

1. 指定项目名称或接受默认设置，确认“complete”文件夹的路径正确无误，然后单击“下一步”。

   ![指定项目名称][CL08]

1. 自定义任何用于导入的目录，然后单击“Next”（下一步）。

   ![选择目录][CL09]

1. 查看要导入的库，然后单击“Next”（下一步）。

   ![查看项目库][CL10]

1. 查看模块结构，然后单击“Next”（下一步）。

   ![查看模块结构][CL11]

1. 指定 JDK，然后单击“Next”（下一步）。

   ![指定 JDK][CL12]

1. 单击“Finish”（完成）。

   ![“完成”按钮][CL13]

IntelliJ 会将 Spring Boot 应用作为项目导入，并在导入完成后显示结构。

![IntelliJ 中的 Spring Boot 应用][CL14]

## <a name="build-your-spring-boot-app"></a>生成 Spring Boot 应用

### <a name="build-the-app-by-using-the-maven-pom"></a>使用 Maven POM 生成应用

1. 如果尚未打开 Maven 工具窗口，请打开它。 单击“视图” > “工具窗口” > “Maven 项目”。

   ![工具窗口和 Maven 项目命令][BU01]

1. 在 Maven 工具窗口中，右键单击“程序包”，然后选择“运行 Maven 生成”。 （如果 Maven 项目未自动显示，请单击 Maven 工具栏上的“重新导入”图标。）

   ![运行 Maven 生成][BU02]

1. 成功创建了 Spring Boot 应用后，IntelliJ 应显示“生成成功”消息。

   ![生成成功消息][BU03]

### <a name="create-a-deployment-ready-artifact"></a>创建随时可用于部署的项目

若要发布 Spring Boot 应用，需要创建一个随时可用于部署的项目。 请执行以下步骤：

1. 在 IntelliJ 中打开你的 Web 应用项目。

1. 依次单击“文件”、“项目结构”。

   ![“项目结构”命令][ART01]

1. 单击绿色加号（“+”）符号添加项目，然后依次单击“JAR”、“空”。

   ![添加项目][ART02]

1. 在确保不添加“.jar”扩展名的同时命名你的项目，然后指定 Maven 输出的目标文件夹。

   ![指定项目属性][ART03]

1. 创建项目清单（可选）：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“Create Manifest”（创建清单）。

      ![单击“创建清单”按钮][ART04a]

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 选择项目的默认路径，然后单击“OK”（确定）。

      ![指定项目路径][ART04b]

   c. 单击省略号“...”找到主类。

      ![找到主类][ART04c]

   d. 选择主类，然后单击“OK”（确定）。

      ![指定主类][ART04d]

1. 单击 **“确定”**。

   ![关闭“项目结构”对话框][ART05]

> [!NOTE]
> 有关在 IntelliJ 中创建项目的详细信息，请参阅 JetBrains 网站上的 [Configuring Artifacts]（配置项目）。
>

### <a name="build-the-artifact-for-deployment"></a>生成要部署的项目

1. 单击“Build”（生成），然后单击“Artifacts”（项目）。

   ![“生成项目”命令][BU04]

1. 当出现“Build Artifact”（生成项目）上下文菜单时，请单击“Build”（生成）。

   ![“生成项目”上下文菜单][BU05]

IntelliJ 应在项目工具窗口中显示 Spring Boot 应用的已完成项目。

   ![创建的项目][BU06]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>使用 Docker 容器将 Web 应用发布到 Azure

1. 如果尚未登录到 Azure 帐户，请执行[适用于 IntelliJ 的 Azure 工具包的登录说明][Azure Sign In for IntelliJ]中的步骤。

1. 在“项目资源管理器”工具窗口中，右键单击该项目，然后选择“Azure” > “Publish as Docker Container”（发布为 Docker 容器）。

   ![“发布为 Docker 容器”命令][PU01]

1. 当显示“在 Azure 上部署 Docker 容器”对话框时，任何现有的 Docker 主机均会显示。 如果选择部署到现有主机，可以跳到步骤 4。 否则，使用以下步骤创建主机：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击绿色加号（“+”）符号。

      ![添加新的 Docker 主机][PU02]

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 当显示“创建 Docker 主机”对话框时，可以选择接受默认设置，也可以为新的 Docker 主机指定任何自定义设置。 （有关各种设置的详细说明，请参阅[使用适用于 IntelliJ 的 Azure 工具包将 Web 应用发布为 Docker 容器][Publish Container with Azure Toolkit]。）在指定了要使用的设置后单击“Next”（下一步）。

      ![指定 Docker 主机选项][PU03a]

   c. 可以选择使用 Azure Key Vault 中的现有登录凭据，也可以选择输入新的 Docker 登录凭据。 在指定了选项后单击“Finish”（完成）。

      ![指定 Docker 主机凭据][PU03b]

1. 选择你的 Docker 主机，然后单击“下一步”。

   ![选择要使用的 Docker 主机][PU04]

1. 在“在 Azure 上部署 Docker 容器”对话框的最后一页上，指定以下选项：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 可以选择为要托管 Docker 容器的容器指定一个自定义名称，也可以接受默认设置。

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 使用以下语法输入 Docker 主机的 TCP 端口：[外部端口]:[内部端口]。 例如，“80:8080”指定外部端口为“80”，默认的内部 Spring Boot 端口为“8080”。
   
      如果已自定义内部端口（例如通过编辑 application.yml 文件自定义），则需要指定端口号以便能够在 Azure 中进行正确路由。

   c. 在配置了这些选项后，单击“完成”。

   ![在 Azure 上部署 Docker 容器][PU05]

1. Azure 工具包完成发布后，Azure 活动日志显示状态为“已发布”。

   ![已成功部署 Docker 主机][PU06]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

若要了解使用 IntelliJ 创建 Spring Boot 应用的其他方法，请参阅 JetBrains 网站上的[创建 Spring Boot 项目](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html)。

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Configuring Artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html
[Deploy Spring Boot on Linux in ACS]:container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png

