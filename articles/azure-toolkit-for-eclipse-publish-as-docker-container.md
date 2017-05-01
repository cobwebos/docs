---
title: "使用用于 Eclipse 的 Azure 工具包发布 Docker 容器 | Microsoft Docs"
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
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>如何使用用于 Eclipse 的 Azure 工具包将 Web 应用发布为 Docker 容器

Docker 容器广泛用于部署 Web 应用程序，开发人员可在其中将其所有项目文件和依赖项整合成单个包，以便部署到服务器。 用于 Eclipse 的 Azure 工具包可以添加用于部署到 Microsoft Azure 的*发布为 Docker 容器*功能，为 Java 开发人员简化了部署过程。本文中的步骤将引导你完成将应用程序作为 Docker 容器发布到 Azure 的过程。

> [!NOTE]
>
> [Docker 网站]上提供了有关 Docker 的详细信息。
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>使用 Docker 容器将 Web 应用发布到 Azure

1. 在 Eclipse 中打开你的 Web 应用项目。

1. 使用以下方法之一启动“发布为 Docker 容器”向导：

   * 在“导航器”视图中右键单击你的项目，然后依次单击“Azure”、“发布为 Docker 容器”： ![发布为 Docker 容器][PUB01]

   * 在 Eclipse 工具栏中单击“发布”菜单，然后单击“发布为 Docker 容器”： ![发布为 Docker 容器][PUB02]

1. 出现“在 Azure 上部署 Docker 容器”向导时，你将看到如下图所示的对话框：![在 Azure 上部署 Docker 容器][PUB03]

   a. 在“Docker 映像名称”文本框中输入 Docker 主机的唯一名称。 （向导会自动创建名称，但如果需要，你可以修改该名称。）

   b.保留“数据库类型”设置，即设置为“共享”。 “主机”窗口将显示已创建的所有 Docker 主机。
      * 如果你尚未创建任何 Docker 主机，对话框的此部分将是空白的。
      * 如果已创建 Docker 主机，可以选择将 Web 应用部署到现有主机；否则，请遵循以下步骤创建新的 Docker 主机。

1. 若要创建新的 Docker 主机，请单击“添加”按钮；此时将启动“创建 Docker 主机”对话框。
      ![在 Azure 向导中部署 Docker 容器][PUB04]

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 指定 Docker 主机的以下选项。 （向导将自动生成大多数选项，但如果你想要自定义任何选项，可对其进行修改。）

      * **名称**：Docker 主机的唯一名称。 （这与前面指定的 Docker 映像名称不同。）

      * **订阅**：指定要为主机使用哪个 Azure 订阅。
      
      * **区域**：指定主机要定位到的地理区域。
      
      * 在“OS 和大小”选项卡上：
         * **主机 OS**：指定包含主机的虚拟机的操作系统。
         * **大小**：指定主机的虚拟机大小。

      * 在“资源组”选项卡上：
         * **新建资源组**：用于创建主机的新资源组。
         * **现有资源组**：用于指定 Azure 帐户中的现有资源组。

      * 在“网络”选项卡上：
         * **新建虚拟网络**：用于创建主机的新虚拟网络。
         * **现有虚拟网络**：用于指定 Azure 帐户中的现有虚拟网络。

      * 在“存储”选项卡上：
         * **新建存储帐户**：用于创建主机的新存储帐户。
         * **现有存储帐户**：用于指定 Azure 帐户中的现有存储帐户。

   b. 指定上述选项后，请单击“下一步”。

   c. 为 Docker 主机的虚拟机登录名选择以下选项之一：   ![创建 Docker 主机][PUB05]

      * **从 Azure Key Vault 导入凭据**：用于指定以前存储在 Azure 订阅中的凭据集。

      > [!NOTE]
      > 共享同一订阅的另一帐户或服务主体不会自动访问使用特定帐户或服务主体创建的 Azure Key Vault。 若要允许另一帐户或服务主体使用 Key Vault，需要使用 Azure 门户添加该帐户或服务主体。

      * **新建登录凭据**：用于创建一组新的登录凭据，这需要在“VM 凭据”选项卡上指定以下选项：
         * **用户名**：指定虚拟机登录名的用户名。
         * **密码**和**确认**：指定虚拟机登录名的密码。
         * **SSH**：指定 Docker 主机的安全外壳 (SSH) 设置；可从以下选项中选择：
            * **无**：指定虚拟机不允许 SSH 连接。
            * **自动生成**：此选项会自动创建用于通过 SSH 建立连接的必需设置。
            * **从目录导入**：用于指定包含以前已保存的一组 SSH 设置的目录。 更具体地说，该目录必须包含以下两个文件：
               * *id_rsa*：此文件包含用户的 RSA 标识。
               * *id_rsa.pub*：此文件包含用于身份验证的 RSA 公钥。
        
      * 在“Docker 守护程序访问”选项卡上指定以下选项：![创建 Docker 主机][PUB06]

         * **Docker 守护程序端口**：指定 Docker 主机的唯一 TCP 端口。
         * **TLS 安全性**：指定 Docker 主机的传输层安全性设置；可从以下选项中选择：
            * **无**：指定虚拟机不允许 TLS 连接。
            * **自动生成**：此选项会自动创建用于通过 TLS 建立连接的必需设置。
            * **从目录导入**：用于指定包含以前已保存的一组 TLS 设置的目录。 更具体地说，该目录必须包含以下六个文件：
               * *ca.pem* 和 *ca key.pem*：这些文件包含 TLS 证书颁发机构的证书和公钥。
               * *cert.pem* 和 *key.pem*：这些文件包含用于 TLS 身份验证的客户端证书和公钥。
               * *server.pem* 和 *server-key.pem*：这些文件包含主机的服务器证书和公钥。

   d. 输入上述所有选项后，请单击“完成”。

1. 再次出现“在 Azure 上部署 Docker 容器”向导时，请单击“下一步”。
   ![在 Azure 向导中部署 Docker 容器][PUB07]

1. 在向导的最后一页中指定以下选项：

   * **Docker 容器名称**：Docker 容器的唯一名称。

   * 选择以下 Docker 映像之一：

      * **预定义的 Docker 映像**：用于指定 Azure 中预先存在的 Docker 映像。

      > [!NOTE]
      > 此下拉菜单中的 Docker 映像列表包括 Azure 工具包已配置为要修补的多个映像，以便能够自动部署你的项目。

      * **自定义 Dockerfile**：用于指定本地计算机中以前保存的 Dockerfile。

      > [!NOTE]
      > 这是一项比较高级的功能，面向想要部署自己的 Dockerfile 的开发人员。 但是，使用此选项的开发人员需负责确保正确生成其 Dockerfile。 Azure 工具包不会验证自定义 Dockerfile 中的内容，因此，如果 Dockerfile 有问题，部署可能会失败。 此外，Azure 工具包预期自定义 Dockerfile 中包含 Web 应用项目，并会尝试打开 HTTP 连接；如果开发人员发布不同类型的项目，在部署后可能会收到无实质影响的错误。

   * **端口设置**：指定 Docker 容器的唯一 TCP 端口绑定。
   ![在 Azure 向导中部署 Docker 容器][PUB08]

1. 完成上述所有步骤后，请单击“完成”。

Azure 工具包随即开始在 Docker 容器中将你的 Web 应用部署到 Azure。 

## <a name="see-also"></a>另请参阅
有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
  * [安装用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的登录说明]
  * [在 Eclipse 中创建 Azure 的 Hello World Web 应用]
* [用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的登录说明]
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关 Docker 的其他资源，请参阅官方 [Docker 网站]。

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安装用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的登录说明]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[用于 IntelliJ 的 Azure 工具包的登录说明]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/

[Docker 网站]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png

