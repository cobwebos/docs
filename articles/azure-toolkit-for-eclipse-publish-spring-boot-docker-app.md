---
title: "使用用于 Eclipse 的 Azure 工具包将 Spring Boot 应用作为 Docker 容器发布 | Microsoft 文档"
description: "了解如何使用用于 Eclipse 的 Azure 工具包将 Web 应用作为 Docker 容器发布到 Microsoft Azure。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>使用用于 Eclipse 的 Azure 工具包将 Spring Boot 应用作为 Docker 容器发布

[Spring Framework] 是一种开源解决方案，可帮助 Java 开发人员创建企业级应用程序。 基于该平台构建的其中一个更常用的项目是 [Spring Boot]，该项目提供了简化的方法来创建独立的 Java 应用程序。

[Docker] 是一种开源解决方案，可帮助开发人员自动部署、扩展和管理在容器中运行的应用程序。

本教程将指导你完成使用用于 Eclipse 的 Azure 工具包将 Spring Boot 应用程序作为 Docker 容器部署到 Microsoft Azure 的步骤。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>克隆默认 Spring Boot Docker 应用存储库

### <a name="importing-the-public-repository"></a>导入公共存储库

以下步骤将指导你完成使用 IntelliJ 将 Spring Boot Docker 存储库克隆到本地计算机的步骤。 如果要使用命令行，请参阅[在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上][Deploy Spring Boot on Linux in ACS]。

1. 打开 Eclipse。

1. 单击“文件”，然后单击“导入”。

   ![文件导入菜单][CL01]

1. “导入”对话框打开后：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 展开 Git。

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 突出显示来自 Git 的项目。
   
   c. 单击“下一步”。

   ![“导入”对话框][CL02]

1. 在“选择存储库源”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 突出显示“克隆 URI”。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“下一步”。

   ![选择存储库源][CL03]

1. 在“源 Git 存储库”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 输入存储库 URI 的 `https://github.com/spring-guides/gs-spring-boot-docker.git`；该操作应该会为“主机”和“存储库路径”字段自动填充正确的值。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 Spring Boot 存储库是公开的，因此，你不必输入 Git 用户名和密码。
   
   c. 单击“下一步”。

   ![源 Git 存储库][CL04]

1. 在“分支”页上，单击“下一步”。

   ![分支][CL05]

1. 在“本地目标”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 指定放置本地存储库的本地文件夹。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“下一步”。

   ![本地目标][CL06]

1. 在“选择用于导入项目的向导”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 选择“作为常规项目导入”。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“下一步”。

   ![本地目标][CL07]

1. 在“导入项目”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 指定“项目名称”。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“完成” 。

   ![导入项目][CL08]

1. 当存储库成功克隆时，你将看到 Eclipse 中列出的所有文件。

   ![本地存储库][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>从本地存储库创建 Maven 项目

Spring Boot Docker 存储库包含一个可用于本教程的完整 Maven 项目。 

1. 单击“文件”，然后单击“导入”。

   ![文件导入菜单][CL01]

1. “导入”对话框打开后：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 展开 Maven。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 突出显示现有 Maven 项目。
   
   c. 单击“下一步”。

   ![“导入”对话框][MV01]

1. 在“Maven 项目”页上：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 在根文件夹的本地存储库中指定 `complete` 文件夹。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 展开“高级”部分，并为“名称模板”输入自定义名称。
   
   c. 选中项目内 `pom.xml` 文件对应的框。
   
   d. 单击“完成”。

   ![Maven 项目][MV02]

1. 当 Maven 项目成功打开后，你将看到 Eclipse 中列出的第二个项目。

   ![本地 Maven 项目][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>使用 Maven 生成 Spring Boot 应用

1. 在 Eclipse 项目资源管理器中，突出显示 Maven 项目。

1. 依次单击“运行”、“运行方式”，然后单击“Maven 生成”。

   ![作为 Maven 生成运行][BU01]

1. 成功构建应用程序后，控制台窗口将列出状态。

   ![Maven 生成成功][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>使用 Docker 容器将 Web 应用发布到 Azure

1. 在 Eclipse 项目资源管理器中，突出显示 Maven 项目。

1. 单击 Azure“发布”菜单，然后单击“发布为 Docker 容器”。

   ![发布为 Docker 容器][PU01]

1. 显示“在 Azure 上部署 Docker 容器”对话框时：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 输入自定义的 Docker 映像名称。
   
   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 对于要部署的项目，指定到刚生成的 `gs-spring-boot-docker-0.1.0.jar` 文件的路径。

   ![指定 Docker 选项][PU02]

   将显示任何现有的 Docker 主机。 如果选择部署到现有主机，可以跳到步骤 4。 否则，就需要使用以下步骤创建一个新主机：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“添加”。

      ![添加新的 Docker 主机][PU03]

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 当显示“创建 Docker 主机”对话框时，可以选择接受默认设置，也可以为新的 Docker 主机指定任何自定义设置。 （有关各种设置的详细说明，请参阅文章[使用用于 IntelliJ 的 Azure 工具包将 Web 应用发布为 Docker 容器][Publish Container with Azure Toolkit]。）在指定了要使用的设置后，单击“下一步”。

      ![指定 Docker 主机选项][PU04]

   c. 可以选择使用 Azure Key Vault 中的现有登录凭据，也可以选择输入新的 Docker 登录凭据。 在指定了选项后单击“完成”。

      ![指定 Docker 主机凭据][PU05]

1. 突出显示你的 Docker 主机，然后单击“下一步”。

   ![选择要使用的 Docker 主机][PU06]

1. 在“在 Azure 上部署 Docker 容器”对话框的最后一页上，需要指定以下选项：

   a.在“横幅徽标”下面，选择“删除上传的徽标”。 可以选择为将要承载你的 Docker 容器的容器指定自定义名称，也可以接受默认值。

   b.在“磁贴徽标”下面，选择“删除上传的徽标”。 需要使用以下语法输入 Docker 主机的 TCP 端口：[外部端口]:[内部端口]。 例如，“80: 8080”指定外部端口为“80”，并且默认的内部 Spring Boot 端口为“8080”。
   
      如果已自定义了内部端口（例如通过编辑 application.yml 文件），则需要指定端口号以便能够在 Azure 中进行正确路由。

   c. 在配置了这些选项后，单击“完成”。

   ![在 Azure 上部署 Docker 容器][PU07]

1. Azure 工具包完成发布后，Azure 活动日志将显示状态为“已发布”。

   ![已成功部署 Docker 主机][PU08]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

