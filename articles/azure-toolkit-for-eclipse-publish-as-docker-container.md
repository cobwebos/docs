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
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 746bb0a073396b84fa8592adda6748a2a5692ee8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---

# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>使用用于 Eclipse 的 Azure 工具包将 Web 应用发布为 Docker 容器

Docker 容器广泛用于部署 Web 应用程序。 开发人员可在其中将其所有项目文件和依赖项整合成单个包，以便部署到服务器。 用于 Eclipse 的 Azure 工具包可以添加用于部署到 Microsoft Azure 的“发布为 Docker 容器”功能，为 Java 开发人员简化了部署过程。** 本文逐步引导你完成将应用程序作为 Docker 容器发布到 Azure 的过程。

> [!NOTE]
> [Docker 网站]上提供了有关 Docker 的详细信息。
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>使用 Docker 容器将 Web 应用发布到 Azure

1. 在 Eclipse 中打开你的 Web 应用项目。

2. 若要启动“发布为 Docker 容器”向导，请执行以下操作之一：****

   * 在“导航器”视图中右键单击你的项目，然后依次单击“Azure”、“发布为 Docker 容器”。****

      ![“导航器”视图中的“发布为 Docker 容器”命令][PUB01]

   * 在 Eclipse 工具栏中单击“发布”按钮，然后单击“发布为 Docker 容器”。****

      ![Eclipse 工具栏中的“发布为 Docker 容器”命令][PUB02]
      
    此时将打开“在 Azure 中部署 Docker 容器”向导。****

    ![“在 Azure 中部署 Docker 容器”向导][PUB03]

3. 在“键入映像名称，选择项目的路径，并检查要使用的 Docker 主机”窗口中执行以下操作：****

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Docker 映像名称”框中输入 Docker 主机的唯一名称。**** （向导会自动创建名称，但你可以修改该名称。）

    b. “主机”区域将显示已创建的所有 Docker 主机。**** 执行下列操作之一：

    * 如果有现有的 Docker 主机，可以在其中部署 Web 应用。
    * 若要创建新的 Docker 主机，请单击“添加”。****  
      
    此时将打开“创建 Docker 主机”对话框。****

    ![“在 Azure 中部署 Docker 容器”向导][PUB04a]

4. 在“配置新虚拟机”窗口中指定 Docker 主机的以下选项。**** （向导会自动生成大多数选项，但你可以修改其中的任何选项。）

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **名称**：输入 Docker 主机的唯一名称。 （这与前面指定的 Docker 映像名称不同。）

   b. **订阅**：输入主机要使用的 Azure 订阅。

   c. **区域**：输入主机所在的地理区域。

   d. 在“主机 OS 和大小”选项卡上：****
     * **主机 OS**：输入包含主机的虚拟机的操作系统。
     * **大小**：输入主机的虚拟机大小。

   e. 在“资源组”选项卡上：****
     * **新建资源组**：为主机创建新的资源组。
     * **现有资源组**：输入 Azure 帐户中的现有资源组。

   f. 在“网络”选项卡上：****
     * **新建虚拟网络**：为主机创建新的虚拟网络。
     * **现有虚拟网络**：输入 Azure 帐户中的现有虚拟网络。

   g. 在“存储”选项卡上：****
     * **新建存储帐户**：为主机创建新的存储帐户。
     * **现有存储帐户**：输入 Azure 帐户中的现有存储帐户。

5. 单击“下一步”。

6. 在“配置登录凭据和端口设置”窗口中，选择以下选项之一：****

    * **从 Azure Key Vault 导入凭据**：指定以前存储在 Azure 订阅中的凭据集。

      >[!NOTE]
      >共享订阅的另一帐户或服务主体不会自动访问使用特定帐户或服务主体创建的 Azure Key Vault。 若要允许另一帐户或服务主体使用 Key Vault，必须使用 Azure 门户添加该帐户或服务主体。

    * **新建登录凭据**：创建一组新的登录凭据。 如果选择此选项，请执行以下操作：
    
      * 在“VM 凭据”选项卡上，为 Docker 主机的虚拟机登录凭据选择以下选项之一：****

          * **用户名**：输入虚拟机登录凭据的用户名。
          * **密码**和**确认**：输入虚拟机登录凭据的密码。
          * **SSH**：输入 Docker 主机的安全外壳 (SSH) 设置。 可从以下选项中选择：
            * **无**：指定虚拟机不允许 SSH 连接。
            * **自动生成**：自动创建用于通过 SSH 建立连接的必需设置。
            * **从目录导入**：指定包含以前已保存的一组 SSH 设置的目录。 该目录必须包含以下两个文件：
                * *id_rsa*：包含用户的 RSA 标识。
                * *id_rsa.pub*：包含用于身份验证的 RSA 公钥。
        
        ![创建 Docker 主机][PUB05]

      * 在“Docker 守护程序凭据”选项卡上指定以下选项：****

          * **Docker 守护程序端口**：输入 Docker 主机的唯一 TCP 端口。
          * **TLS 安全性**：输入 Docker 主机的传输层安全性设置。 可从以下选项中选择：
            * **无**：指定虚拟机不允许 TLS 连接。
            * **自动生成**：自动创建用于通过 TLS 建立连接的必需设置。
            * **从目录导入**：指定包含以前已保存的一组 TLS 设置的目录。 更具体地说，该目录必须包含以下六个文件：
                * *ca.pem* 和 *ca key.pem*：包含 TLS 证书颁发机构的证书和公钥。
                * *cert.pem* 和 *key.pem*：包含用于 TLS 身份验证的客户端证书和公钥。
                * *server.pem* 和 *server-key.pem*：包含主机的服务器证书和公钥。

        ![创建 Docker 主机][PUB06]

7. 输入前面的所有信息后，单击“完成”。****

8. 在“在 Azure 中部署 Docker 容器”向导中，单击“下一步”。****

   ![“在 Azure 中部署 Docker 容器”向导][PUB07]

9. 在“配置要创建的 Docker 容器”窗口中执行以下操作：****

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Docker 容器名称”框中，输入 Docker 容器的唯一名称。****

   b. 选择以下 Docker 映像之一：
     * **预定义的 Docker 映像**：指定 Azure 中预先存在的 Docker 映像。

       >[!NOTE]
       >此框中的 Docker 映像列表包括 Azure 工具包已配置为要修补的多个映像，以便能够自动部署项目。

     * **自定义 Dockerfile**：指定本地计算机中以前保存的 Dockerfile。

       >[!NOTE]
       >这是一项比较高级的功能，面向想要部署自己的 Dockerfile 的开发人员。 但是，使用此选项的开发人员需负责确保正确生成其 Dockerfile。 Azure 工具包不会验证自定义 Dockerfile 中的内容，因此，如果 Dockerfile 有问题，部署可能会失败。 此外，Azure 工具包预期自定义 Dockerfile 中包含 Web 应用项目，因此会尝试打开 HTTP 连接。 如果开发人员发布不同类型的项目，在部署后可能会收到无实质影响的错误。

   c. **端口设置**：输入 Docker 容器的唯一 TCP 端口绑定。

     ![“配置要创建的 Docker 容器”窗口][PUB08]

10. 完成前面的所有步骤后，单击“完成”。****

Azure 工具包随即开始在 Docker 容器中将你的 Web 应用部署到 Azure。 

## <a name="next-steps"></a>后续步骤
有关用于 Java IDE 的 Azure 工具包的详细信息，请参阅以下资源：

* [用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
  * [安装用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的登录说明]
  * [在 Eclipse 中创建适用于 Azure 的 Hello World Web 应用]
* [用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的登录说明]
  * [在 IntelliJ 中创建适用于 Azure 的 Hello World Web 应用]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关 Docker 的其他资源，请参阅官方 [Docker 网站]。

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建适用于 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建适用于 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
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
