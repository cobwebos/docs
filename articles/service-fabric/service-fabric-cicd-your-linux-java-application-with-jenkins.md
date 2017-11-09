---
title: "使用 Jenkins 实现 Azure Service Fabric Linux Java 应用程序的持续生成和集成 | Microsoft Docs"
description: "使用 Jenkins 实现 Linux Java 应用程序的持续生成和集成"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.openlocfilehash: d9870fafab3df3ab0ec72305e76a4d3547cc5b2c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>使用 Jenkins 生成和部署 Linux Java 应用程序
Jenkins 是流行的应用持续集成和部署工具。 本文介绍如何使用 Jenkins 生成和部署 Azure Service Fabric 应用程序。

## <a name="general-prerequisites"></a>常规先决条件
- 已在本地安装 Git。 可以根据操作系统[从 Git 下载页](https://git-scm.com/downloads)安装相应的 Git 版本。 如果是 Git 的新手，请通过 [Git 文档](https://git-scm.com/docs)了解其详细信息。
- 已准备好 Service Fabric Jenkins 插件。 可从 [Service Fabric 下载页](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下载该插件。

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 群集中设置 Jenkins

可在 Service Fabric 群集内部或外部设置 Jenkins。 以下各部分展示了如何在群集内部对其进行设置，同时使用 Azure 存储帐户保存容器实例的状态。

### <a name="prerequisites"></a>先决条件
1. 准备好 Service Fabric Linux 群集。 通过 Azure 门户创建的 Service Fabric 群集已安装 Docker。 如果在本地运行群集，可使用 ``docker info`` 命令检查是否已安装 Docker。 如果未安装，请相应地使用以下命令安装它：

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > 确保将 8081 端口指定为群集上的自定义终结点。
   >
2. 通过执行以下步骤克隆应用程序：

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
cd service-fabric-java-getting-started/Services/JenkinsDocker/
```

3. 保留文件共享中 Jenkins 容器的状态：
  * 使用名称（如 ``sfjenkinsstorage1``）在群集所在的**同一区域**中创建 Azure 存储帐户。
  * 使用名称（如 ``sfjenkins``）在该存储帐户下创建一个**文件共享**。
  * 针对文件共享单击“连接”，并记下它在“从 Linux 进行连接”下显示的值，该值应如下所示：
```sh
sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
```

> [!NOTE]
> 必须在群集节点中安装 cifs-utils 包，才能安装 cifs 共享。         
>

4. 从步骤 3 使用 azure 存储详细信息更新 ```setupentrypoint.sh``` 脚本中的占位符值。
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * 将 ``[REMOTE_FILE_SHARE_LOCATION]`` 替换为前面步骤 3 中的连接输出中的值 ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins``。
  * 将 ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` 替换为前面步骤 3 中的值 ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777``。

5. 连接到群集并安装容器应用程序。
```sh
sfctl cluster select --endpoint http://PublicIPorFQDN:19080   # cluster connect command
bash Scripts/install.sh
```
这会在群集上安装 Jenkins 容器，可以使用 Service Fabric Explorer 监视该容器。

   > [!NOTE]
   > 可能需要几分钟时间在群集上下载 Jenkins 映像。
   >

### <a name="steps"></a>步骤
1. 在浏览器中转到 ``http://PublicIPorFQDN:8081``。 该 URL 提供了登录时所需的初始管理员密码的路径。 
2. 查看 Service Fabric 资源管理器确定 Jenkins 容器在哪一节点上运行。 通过安全外壳 (SSH) 登录到此节点。
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. 使用 ``docker ps -a`` 获取容器实例 ID。
4. 通过安全外壳 (SSH) 登录到该容器，并粘贴 Jenkins 门户中显示的路径。 例如，如果门户中显示路径为 `PATH_TO_INITIAL_ADMIN_PASSWORD`，则可运行以下命令：

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. 在“Jenkins 入门”页上，选择“选择要安装的插件”选项，选择“无”复选框，单击“安装”。
6. 创建用户，或选择以管理员身份继续。

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 群集外部设置 Jenkins

可在 Service Fabric 群集内部或外部设置 Jenkins。 以下部分说明如何在群集外部设置 Jenkins。

### <a name="prerequisites"></a>先决条件
需安装 Docker。 可在终端中使用以下命令安装 Docker：

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

现在，在终端中运行 ``docker info`` 时，输出中应会显示 Docker 服务正在运行。

### <a name="steps"></a>步骤
  1. 拉取 Service Fabric Jenkins 容器映像：``docker pull raunakpandya/jenkins:v1``
  2. 运行容器映像：``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. 获取容器映像实例的 ID。 可以使用命令 ``docker ps –a`` 列出所有 Docker 容器
  4. 执行以下步骤登录到 Jenkins 门户：

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    如果容器 ID 为 2d24a73b5964，请使用 2d24。
    * 从门户 ``http://<HOST-IP>:8080`` 登录到 Jenkins 仪表板时需要此密码
    * 首次登录后，可以创建自己的用户帐户供将来使用，或者继续使用管理员帐户。 创建用户后，需要继续使用该用户。
  5. 使用 [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)（生成新的 SSH 密钥并将其添加到 SSH 代理）中所述的步骤，将 GitHub 设置为使用 Jenkins。
        * 根据 GitHub 提供的说明生成 SSH 密钥，然后将 SSH 密钥添加到托管存储库的 GitHub 帐户。
        * 在 Jenkins Docker shell（而不是主机）中运行上述链接中提到的命令。
      * 若要从主机登录到 Jenkins shell，请使用以下命令：

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

确保托管 Jenkins 容器映像的群集或计算机使用公共 IP。 这样，Jenkins 实例便可以从 GitHub 接收通知。

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>通过门户安装 Service Fabric Jenkins 插件

1. 转到 ``http://PublicIPorFQDN:8081``
2. 在 Jenkins 仪表板中，选择“管理 Jenkins” > “管理插件” > “高级”。
可在此处上传插件。 选择“选择文件”，并选择已根据先决条件所述下载的“serviceFabric.hpi”文件，也可以在[此处](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下载。 选择 **上传** 时，Jenkins 会自动安装该插件。 如果系统请求重新启动，请允许。

## <a name="create-and-configure-a-jenkins-job"></a>创建和配置 Jenkins 作业

1. 通过仪表板创建**新项**。
2. 输入项名称（例如 **MyJob**）。 选择“自由格式的项目”，并单击“确定”。
3. 转到作业页，单击“配置”。

   a. 在常规部分中，选择“GitHub 项目”所对应的复选框，指定 GitHub 项目 URL。 此 URL 托管要与 Jenkins 持续集成和持续部署 (CI/CD) 流（例如 ``https://github.com/sayantancs/SFJenkins``）集成的 Service Fabric Java 应用程序。

   b. 在“源代码管理”部分，选择 **Git**。 指定用于托管要与 Jenkins CI/CD 流（例如 ``https://github.com/sayantancs/SFJenkins.git``）集成的 Service Fabric Java 应用程序的存储库 URL。 也可在此处指定要生成的分支（例如 **/master**）。
4. 配置 *GitHub*（存储库的托管位置），使它能够与 Jenkins 通信。 请执行以下步骤：

   a. 转到 GitHub 存储库页。 转到“设置” > “集成和服务”。

   b. 选择“添加服务”，键入 **Jenkins**，并选择“Jenkins-GitHub 插件”。

   c. 输入 Jenkins Webhook URL（默认为 ``http://<PublicIPorFQDN>:8081/github-webhook/``）。 单击“添加/更新服务”。

   d. 将向 Jenkins 实例发送一个测试事件。 GitHub 中的 Webhook 旁边应会显示一个绿色复选标记，表示可以生成项目。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“生成触发器”部分下面，选择所需的生成选项。 在此示例中，我们希望每当向存储库推送信息，就会触发生成。 为此，可以选择“用于 GITScm 轮询的 GitHub 挂钩触发器”。 （以前，此选项称为“向 GitHub 推送更改时生成”。）

   f. 在“生成”部分下面，从“添加生成步骤”下拉列表中选择“调用 Gradle 脚本”。 在出现的小组件中，打开高级菜单，为应用程序指定“根生成脚本”的路径。 该脚本将从指定的路径中选择 build.gradle，然后执行相应的操作。 如果创建名为 ``MyActor`` 的项目（使用 Eclipse 插件或 Yeoman 生成器），则根生成脚本应包含 ``${WORKSPACE}/MyActor``。 有关工作方式的示例，请参阅以下屏幕截图：

    ![Service Fabric Jenkins 生成操作][build-step]

   g. 在“生成后操作”下拉列表中，选择“部署 Service Fabric 项目”。 此处需要提供有关在何处部署 Jenkins 编译的 Service Fabric 应用程序的群集详细信息。 还可以提供其他用于部署应用程序的应用程序详细信息。 有关工作方式的示例，请参阅以下屏幕截图：

    ![Service Fabric Jenkins 生成操作][post-build-step]

   > [!NOTE]
   > 如果使用 Service Fabric 部署 Jenkins 容器映像，此处的群集可与托管 Jenkins 容器应用程序的群集相同。
   >

## <a name="next-steps"></a>后续步骤
现已配置 GitHub 和 Jenkins。 请考虑对存储库示例 https://github.com/sayantancs/SFJenkins 中的 ``MyActor`` 项目进行一些示例更改。 将更改推送到远程 ``master`` 分支（或配置使用的任何分支）。 这会触发配置的 Jenkins 作业 ``MyJob``。 它会从 GitHub 提取更改、生成这些更改并将应用程序部署到在生成后操作中指定的群集终结点。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png
