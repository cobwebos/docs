---
title: "使用 Jenkins 针对 Linux Java 应用程序进行持续生成和部署 | Microsoft 文档"
description: "使用 Jenkins 针对 Linux Java 应用程序进行持续生成和部署"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>使用 Jenkins 生成和部署 Linux Java 应用程序
Jenkins 是常用的持续集成和部署工具。 本文档介绍如何使用 Jenkins 生成和部署 Service Fabric 应用程序。

## <a name="general-prerequisites"></a>常规先决条件
1. 需在本地安装 git。 可以根据 OS 从[此处](https://git-scm.com/downloads)安装适当的 git 版本。 如果不熟悉 git，可以执行[文档](https://git-scm.com/docs)中提及的步骤，自行熟悉 git。
2. 需准备好 Service Fabric Jenkins 插件。 从[此处](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi)下载 Service Fabric Jenkins 插件。

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 群集中设置 Jenkins

### <a name="prerequisites"></a>先决条件
1. 准备好 Service Fabric Linux 群集。 通过 Azure 门户创建的 Service Fabric 群集已安装 Docker。 如果运行的是本地群集，请使用命令 ``docker info`` 查看 Docker 是否已安装。如果未安装，则请使用以下命令相应地进行安装：
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. 在群集 ``http://PublicIPorFQDN:19080`` 上部署 Service Fabric 容器应用程序。 可执行以下步骤：
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  这样就会将 Jenkins 容器安装到连接的群集。 可以使用 Service Fabric Explorer 监视容器应用程序。

### <a name="steps"></a>步骤
1. 从浏览器转到 URL ``http://PublicIPorFQDN:8081``。 该 URL 会提供登录所需的初始管理员密码的路径。 可以继续使用 Jenkins 作为管理员用户，也可以在使用初始管理员帐户登录后创建和更改用户。
> [!NOTE]
> 需确保在创建群集时将端口 8081 指定为应用程序终结点端口
>

2. 使用 ``docker ps -a`` 获取容器实例 ID。
3. 使用该 ID 以 SSH 方式登录到容器，粘贴在 Jenkins 门户中显示的路径。 例如，如果在门户中显示的路径为 `PATH_TO_INITIAL_ADMIN_PASSWORD`，则可执行以下命令：  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. 根据[此文档](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)的说明设置 GitHub，使之适用于 Jenkins。
    * 根据 GitHub 中提供的说明生成 SSH 密钥，然后将 SSH 密钥添加到托管存储库的 GitHub 帐户。
    * 在 Jenkins-docker 外壳程序中（不是在主机上）运行上述链接中提到的命令
    * 若要从主机登录到 Jenkins 外科程序，需执行以下命令：
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 群集外设置 Jenkins

### <a name="prerequisites"></a>先决条件
1. 需安装 docker。 如果尚未安装 docker，可以在终端键入以下命令进行安装。
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
现在，当用户在终端运行 ``docker info`` 时，就会在输出中看到 docker 服务正在运行。

### <a name="steps"></a>步骤
  1. 拉取 Service Fabric Jenkins 容器：``docker pull IMAGE_NAME ``
  2. 运行容器映像：``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. 获取包含 Jenkins 映像（刚安装）的 docker 容器的 ID。 可以使用命令 ``docker ps –a`` 列出所有 docker 容器
  4. 获取 Jenkins 帐户的管理员密码。 为此，可以执行以下命令：
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    在这里，如果容器 ID 为 2d24a73b5964，则只需插入 2d24
    * 从门户 ``http://<HOST-IP>:8080`` 登录到 Jenkins 仪表板时，将需要此密码
    * 首次登录以后，即可创建自己的用户帐户以备将来之用，也可继续使用管理员帐户。 一旦创建新用户，接下来需使用这个新用户。
  5. 根据[此文档](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)的说明设置 GitHub，使之适用于 Jenkins。
      * 根据 GitHub 中提供的说明生成 SSH 密钥，然后将 SSH 密钥添加到托管存储库的 GitHub 帐户。
      * 在 Jenkins-docker 外壳程序中（不是在主机上）运行上述链接中提到的命令
      * 若要从主机登录到 Jenkins 外科程序，需执行以下命令：
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> 请确保托管 Jenkins 容器映像的群集/计算机使用面向公众的 IP，以便 Jenkins 实例接收来自 GitHub 的通知。
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>从门户安装 Service Fabric Jenkins 插件

1. 转到 ``http://PublicIPorFQDN:8081``
2. 在 Jenkins 仪表板中，选择``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``。
可以在这里上载插件。 选择``Choose file``选项，然后选择 serviceFabric.hpi 文件（已在“先决条件”部分下载）。 一旦用户选择“上载”，Jenkins 就会自动为其安装插件。 也可能会要求用户重新启动，请允许。

## <a name="creating-and-configuring-a-jenkins-job"></a>创建和配置 Jenkins 作业

1. 从仪表板创建``new item``
2. 输入新项目名称（例如 ``MyJob``），选择“自由样式项目”，然后单击“确定”
3. 然后转到作业页，单击``Configure`` -
  * 在常规部分的``Github project``下指定 GitHub 项目 URL，以便托管用于集成 Jenkins CI/CD 流（例如 ``https://github.com/sayantancs/SFJenkins``）的 Service Fabric Java 应用程序。
  * 在``Source Code Management``部分选择``Git``。 指定存储库 URL，以便托管用于集成 Jenkins CI/CD 流（例如 ``https://github.com/sayantancs/SFJenkins.git``）的 Service Fabric Java 应用程序。 也可在此处指定要生成的分支，例如 ``*/master``。
4. 执行以下步骤，以便配置 *Github*（即托管存储库的地方），使之能够与 Jenkins 通信：
  - 转到 GitHub 存储库页。 转到``Settings`` -> ``Integrations and Services``。
  - 选择``Add Service``，键入“Jenkins”，然后选择``Jenkins-Github plugin``。
  - 输入 Jenkins Webhook URL（默认为 ``http://<PublicIPorFQDN>:8081/github-webhook/``）。 单击“添加/更新服务”。
  - 将向 Jenkins 实例发送一个测试事件。 用户会在 GitHub 中 Webhook 的旁边看到一个绿色复选标记，说明可以生成项目！
  - 在``Build Triggers``部分选择需要的生成选项。就此用例来说，我们计划在有内容推送到存储库时触发一个生成，因此相应的选项为``GitHub hook trigger for GITScm polling``（以前为“将更改推送到 GitHub 时生成”）
  - 在``Build section``下面 - 从下拉列表``Add build step``中选择选项``Invoke Gradle Script``。 在附带的小组件中，为应用程序指定``Root build script``的路径。 它将从指定的路径中选取 build.gradle，然后进行相应的操作。 请注意，如果创建名为``MyActor``的项目（使用 Eclipse 插件或 Yeoman 生成器），则根生成脚本应包含``${WORKSPACE}/MyActor``。 例如，此部分很可能如下所示：

    ![Service Fabric Jenkins 生成操作][build-step]
  - 在``Post-Build Actions``下拉列表中，选择``Deploy Service Fabric Project``。 此处需提供群集详细信息（在何处部署 Jenkins 编译的 Service Fabric 应用程序）以及其他应用程序详细信息（用于部署应用程序）。 可以使用以下屏幕截图作为参考：

    ![Service Fabric Jenkins 生成操作][post-build-step]

 >[!Note]
 > 如果使用 Service Fabric 部署 Jenkins 容器映像，则此处的群集可以与托管 Jenkins 容器应用程序的群集相同。
 >

### <a name="end-to-end-scenario"></a>端到端方案
现在已配置 GitHub 和 Jenkins，因此可以在存储库（例如 https://github.com/sayantancs/SFJenkins）的``MyActor``项目中进行一些示例性的更改，并将所做的更改推送到远程``master``分支（或任何已配置的可以使用的分支）。 现在将会触发已配置的 Jenkins 作业``MyJob``。 其基本功能包括：从 GitHub 提取更改、生成这些更改并将应用程序部署到在生成后操作中指定的群集终结点。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

