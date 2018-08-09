---
title: 使用 Jenkins 针对 Azure Service Fabric Linux 应用程序进行持续生成和集成 | Microsoft Docs
description: 使用 Jenkins 针对 Service Fabric Linux 应用程序进行持续生成和集成
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 0de62b6fa05ccad1977e7d98a614e8d601409f5b
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390171"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>使用 Jenkins 生成和部署 Linux 应用程序
Jenkins 是流行的应用持续集成和部署工具。 本文介绍如何使用 Jenkins 生成和部署 Azure Service Fabric 应用程序。

## <a name="topic-overview"></a>主题概述
本文介绍设置 Jenkins 环境的几种可行方法以及生成应用程序后将其部署到 Service Fabric 群集的不同方法。 按照下列常规步骤成功设置 Jenkins，从 GitHub 拉取更改，生成应用程序，以及将其部署到群集：

1. 请确保安装[必备组件](#prerequisites)。
2. 然后，按照以下对应部分的步骤设置 Jenkins：
   * [在 Service Fabric 群集中设置 Jenkins](#set-up-jenkins-inside-a-service-fabric-cluster)， 
   * [在 Service Fabric 群集外部设置 Jenkins](#set-up-jenkins-outside-a-service-fabric-cluster)，或者
   * [在现有 Jenkins 环境中安装 Service Fabric 插件](#install-service-fabric-plugin-in-an-existing-jenkins-environment)。
3. 设置 Jenkins 后，按照[创建和配置 Jenkins 作业](#create-and-configure-a-jenkins-job)中的步骤将 GitHub 设置为在应用程序发生更改时触发 Jenkins 并且通过生成步骤将 Jenkins 作业管道配置为从 GitHub 拉取更改并生成应用程序。 
4. 最后，配置 Jenkins 作业生成后步骤，将应用程序部署到 Service Fabric 群集。 有两种方法可将 Jenkins 配置为将应用程序部署到群集：    
   * 对于开发和测试环境，使用[通过群集管理终结点配置部署](#configure-deployment-using-cluster-management-endpoint)。 这是设置最简单的部署方法。
   * 对于生产环境，使用[通过 Azure 凭据配置部署](#configure-deployment-using-azure-credentials)。 Microsoft 建议对生产环境使用此方法，因为借助 Azure 凭据，可以限制 Jenkins 作业对 Azure 资源的访问权限。 

## <a name="prerequisites"></a>先决条件

- 确保本地安装 Git。 可以根据操作系统[从 Git 下载页](https://git-scm.com/downloads)安装相应的 Git 版本。 如果你是 Git 的新用户，请通过 [Git 文档](https://git-scm.com/docs)了解详细信息。
- 本文章使用 GitHub 上的 Service Fabric 入门示例：[https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) 适用于生成和部署应用程序。 可以创建此存储库的分支来按照说明进行操作，也可以使用自己的 GitHub 项目并对说明进行相应修改。


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>在现有 Jenkins 环境中安装 Service Fabric 插件
如果要将 Service Fabric 插件添加到现有 Jenkins 环境，需要以下条件：

- [Service Fabric CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > 请确保在系统级别（而不是用户级别）安装 CLI，使 Jenkins 可以运行 CLI 命令。 
   >

- 若要部署 Java 应用程序，请同时安装 [Gradle 和 Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development)。 
- 若要部署 .NET Core 2.0 应用程序，请安装 [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development)。 

安装环境所需的必备组件后，可以在 Jenkins 市场中搜索 Azure Service Fabric 插件并安装它。

安装插件后，跳到[创建和配置 Jenkins 作业](#create-and-configure-a-jenkins-job)。


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>在 Service Fabric 群集中设置 Jenkins

可在 Service Fabric 群集内部或外部设置 Jenkins。 以下各部分展示了如何在群集内部对其进行设置，同时使用 Azure 存储帐户保存容器实例的状态。

### <a name="prerequisites"></a>先决条件
- 准备好已安装 Docker 的 Service Fabric Linux 群集。 在 Azure 中运行的 Service Fabric 群集已安装 Docker。 如果要在本地（OneBox 开发环境）运行群集，请使用 `docker info` 命令检查计算机上是否安装了 Docker。 如果未安装，请使用以下命令安装它：

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > 确保将 8081 端口指定为群集上的自定义终结点。 如果使用本地群集，请确保已在主机上打开端口 8081，并且有面向公众的 IP 地址。
   >

### <a name="steps"></a>步骤
1. 通过执行以下命令克隆应用程序：
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. 保留文件共享中 Jenkins 容器的状态：
   1. 使用名称（如 `sfjenkinsstorage1`）在群集所在的**同一区域**中创建 Azure 存储帐户。
   2. 使用名称（如 `sfjenkins`）在该存储帐户下创建一个**文件共享**。
   3. 针对文件共享单击“连接”，并记下它在“从 Linux 进行连接”下显示的值，该值应如下所示：

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > 必须在群集节点中安装 cifs-utils 包，才能安装 cifs 共享。      
   >

4. 使用步骤 2 中获得的 azure 存储详细信息更新 `setupentrypoint.sh` 脚本中的占位符值。
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * 将 `[REMOTE_FILE_SHARE_LOCATION]` 替换为前面步骤 2 中的连接输出中的值 `//sfjenkinsstorage1.file.core.windows.net/sfjenkins`。
   * 将 `[FILE_SHARE_CONNECT_OPTIONS_STRING]` 替换为前面步骤 2 中的值 `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`。

5. **仅安全群集** 
   
   若要从 Jenkins 在安全群集上配置应用程序的部署，必须可从 Jenkins 容器中访问群集证书。 在 ApplicationManifest.xml 文件的“ContainerHostPolicies”标记下，添加此证书引用并使用群集证书值更新指纹值。

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   此外，在 ApplicationManifest.xml 文件的“ApplicationManifest”（根）标记下添加以下行，并使用群集证书值更新指纹值。

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. 连接到群集并安装容器应用程序。

   **安全群集**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   前一个命令采用 PEM 格式的证书。 如果证书为 PFX 格式，可以使用以下命令进行转换。 如果 PFX 文件不受密码保护，请将“passin”参数指定为 `-passin pass:`。
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **非安全群集**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   这会在群集上安装 Jenkins 容器，可以使用 Service Fabric Explorer 监视该容器。

   > [!NOTE]
   > 可能需要几分钟时间在群集上下载 Jenkins 映像。
   >

7. 在浏览器中转到 `http://PublicIPorFQDN:8081`。 该 URL 提供了登录时所需的初始管理员密码的路径。 
2. 查看 Service Fabric 资源管理器确定 Jenkins 容器在哪一节点上运行。 通过安全外壳 (SSH) 登录到此节点。
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. 使用 `docker ps -a` 获取容器实例 ID。
4. 通过安全外壳 (SSH) 登录到该容器，并粘贴 Jenkins 门户中显示的路径。 例如，如果门户中显示路径为 `PATH_TO_INITIAL_ADMIN_PASSWORD`，则可运行以下命令：

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. 在“Jenkins 入门”页上，选择“选择要安装的插件”选项，选择“无”复选框，单击“安装”。
6. 创建用户，或选择以管理员身份继续。

设置 Jenkins 后，跳到[创建和配置 Jenkins 作业](#create-and-configure-a-jenkins-job)。  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>在 Service Fabric 群集外部设置 Jenkins

可在 Service Fabric 群集内部或外部设置 Jenkins。 以下部分说明如何在群集外部设置 Jenkins。

### <a name="prerequisites"></a>先决条件
- 确保计算机上安装了 Docker。 可在终端中使用以下命令安装 Docker：

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  在终端中运行 `docker info` 时，输出应会显示 Docker 服务正在运行。

### <a name="steps"></a>步骤
1. 拉取 Service Fabric Jenkins 容器映像：`docker pull rapatchi/jenkins:latest`。 此映像附带了预安装的 Service Fabric Jenkins 插件。
2. 运行容器映像：`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. 获取容器映像实例的 ID。 可以使用命令 `docker ps –a` 列出所有 Docker 容器
4. 执行以下步骤登录到 Jenkins 门户：

   1. 从主机登录到 Jenkins shell。 使用容器 ID 的前四个数字。 例如，如果容器 ID 为 `2d24a73b5964`，则使用 `2d24`。

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. 从 Jenkins shell 获取适用于容器实例的管理员密码：

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. 若要登录到 Jenkins 仪表板，请在 web 浏览器中打开以下 URL：`http://<HOST-IP>:8080`。 使用上一步的密码解锁 Jenkins。
   4. （可选。）首次登录后，可以创建自己的用户帐户供后续步骤使用，或者继续使用管理员帐户。 如果创建了一个用户，则需要继续使用该用户。
5. 使用[生成新的 SSH 密钥并将其添加到 SSH 代理](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)中的步骤，将 GitHub 设置为使用 Jenkins。
   * 根据 GitHub 提供的说明生成 SSH 密钥，然后将 SSH 密钥添加到托管存储库的 GitHub 帐户。
   * 在 Jenkins Docker shell（而不是主机）中运行上述链接中提到的命令。
   * 若要从主机登录到 Jenkins shell，请使用以下命令：

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

确保托管 Jenkins 容器映像的群集或计算机使用公共 IP 地址。 这样，Jenkins 实例便可以从 GitHub 接收通知。

设置 Jenkins 后，转到下一部分，[创建和配置 Jenkins 作业](#create-and-configure-a-jenkins-job)。

## <a name="create-and-configure-a-jenkins-job"></a>创建和配置 Jenkins 作业

此部分中的步骤演示如何配置 Jenkins 作业以响应 GitHub 存储库中的更改，提取更改，并生成它们。 本部分结束时，会执行最后的步骤，也就是根据选择的部署环境配置作业，使其将应用程序部署到开发/测试环境或生产环境。 

1. 在 Jenkins 仪表板上，单击“新建项”。
2. 输入项名称（例如 **MyJob**）。 选择“自由格式的项目”，并单击“确定”。
3. “作业配置”页面会打开。 （若要获取 Jenkins 仪表板的配置，请单击该作业，并单击“配置”）。

4. 在“常规”选项卡上，选择“GitHub 项目”框，并指定 GitHub 项目 URL。 此 URL 托管要与 Jenkins 持续集成和持续部署 (CI/CD) 流（例如 `https://github.com/{your-github-account}/service-fabric-java-getting-started`）集成的 Service Fabric Java 应用程序。

5. 在“源代码管理”选项卡上，选择“Git”。 指定用于托管要与 Jenkins CI/CD 流（例如 `https://github.com/{your-github-account}/service-fabric-java-getting-started`）集成的 Service Fabric Java 应用程序的存储库 URL。 还可以指定要生成的分支（例如，`/master`）。
6. 将 GitHub 存储库配置为与 Jenkins 通信：

   a. 在 GitHub 存储库页上，转到“设置” > “集成和服务”。

   b. 选择“添加服务”，键入 **Jenkins**，并选择“Jenkins-GitHub 插件”。

   c. 输入 Jenkins Webhook URL（默认为 `http://<PublicIPorFQDN>:8081/github-webhook/`）。 单击“添加/更新服务”。

   d. 将向 Jenkins 实例发送一个测试事件。 GitHub 中的 Webhook 旁边应会显示一个绿色复选标记，表示可以生成项目。

7. 在 Jenkins 的“生成触发器”选项卡上，选择所需的生成选项。 在此示例中，需要在推送到存储库时触发生成，所以选择“用于 GITScm 轮询的 GitHub 挂钩触发器”。 （以前，此选项称为“向 GitHub 推送更改时生成”。）
8. 在“生成”选项卡上，执行下列操作之一，具体取决于是要生成 Java 应用程序还是 .NET Core 应用程序：

   * **对于 Java 应用程序：** 从“添加生成步骤”下拉列表，选择“调用 Gradle 脚本”。 单击“高级”。 在高级菜单中，为应用程序指定“根生成脚本”的路径。 该脚本将从指定的路径中选择 build.gradle，然后执行相应的操作。 对于 [ActorCounter 应用程序](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)，这是 `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`。

     ![Service Fabric Jenkins 生成操作][build-step]

   * **对于 .NET Core 应用程序：** 从“添加生成步骤”下拉列表，选择“执行 Shell”。 在出现的命令框中，首先需要将目录的路径更改为 build.sh 文件所在的位置。 更改目录后即可运行 build.sh 脚本，并将生成应用程序。

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     以下屏幕截图显示了一个命令示例，这些命令用于生成[计 数器服务](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService)示例，其 Jenkins 作业名称为 CounterServiceApplication。

      ![Service Fabric Jenkins 生成操作][build-step-dotnet]

9. 若要将 Jenkins 配置为在生成后操作中将应用部署到 Service Fabric 群集，需要 Jenkins 容器中群集的证书的位置。 基于 Jenkins 容器是在群集内还是群集外运行，选择以下选项之一，并记录群集证书的位置：

   * **对于在群集内运行的 Jenkins：** 可以通过从容器内部回显 Certificates_JenkinsOnSF_Code_MyCert_PEM环境变量的值找到证书的路径。

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **对于在群集外运行的 Jenkins：** 按照以下步骤将群集证书复制到容器：
      1. 证书必须为 PEM 格式。 如果没有 PEM 文件，可以从证书 PFX 文件创建。 如果 PFX 文件不受密码保护，请从主机运行以下命令：

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      如果 PFX 文件受密码保护，将密码添加在 `-passin` 参数中。 例如：

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. 若要获取 Jenkins 容器的容器 ID，请从主机运行 `docker ps`。
      3. 使用以下 Docker 命令，将 PEM 文件复制到容器：
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

即将完成！ 保持 Jenkins 作业的打开状态。 剩下的唯一任务是将生成后步骤配置为将应用程序部署到 Service Fabric 群集：

* 若要部署到开发或测试环境，请按照[使用群集管理终结点配置部署](#configure-deployment-using-cluster-management-endpoint)中的步骤。
* 若要部署到生产环境，请按照[使用 Azure 凭据配置部署](#configure-deployment-using-azure-credentials)中的步骤。

## <a name="configure-deployment-using-cluster-management-endpoint"></a>使用群集管理终结点配置部署
对于开发和测试环境，可以使用群集管理终结点部署应用程序。 使用群集管理终结点来配置生成后操作以部署应用程序，这种方式需要的设置操作最少。 如果要部署到生产环境，请跳到[使用 Azure 凭据配置部署](#configure-deployment-using-azure-credentials)，将 Azure Active Directory 服务主体配置为在部署期间使用。    

1. 在 Jenkins 作业中，单击“生成后操作”选项卡。 
2. 在“生成后操作”下拉列表中，选择“部署 Service Fabric 项目”。 
3. 在“Service Fabric 群集配置”下，选择“填充 Service Fabric 管理终结点”单选按钮。
4. 对于“管理主机”，请输入群集的连接终结点，例如 `{your-cluster}.eastus.cloudapp.azure.com`。
5. 对于“客户端密钥”和“客户端证书”，在 Jenkins 容器中输入 PEM 文件的位置，例如 `/var/jenkins_home/clustercert.pem`。 （复制[创建和配置 Jenkins 作业](#create-and-configure-a-jenkins-job)最后一步的证书位置。）
6. 在“应用程序配置”下，配置“应用程序名称”、“应用程序类型”和（相对）“应用程序清单路径”字段。

   ![Service Fabric Jenkins 生成后操作配置管理终结点](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. 单击“验证配置”。 成功验证后，单击“保存”。 Jenkins 作业管道现在已完全配置。 跳到[后续步骤](#next-steps)测试部署。

## <a name="configure-deployment-using-azure-credentials"></a>使用 Azure 凭据配置部署
对于生产环境，强烈建议将 Azure 凭据配置为部署应用程序。 本部分演示如何配置要使用的 Azure Active Directory 服务主体，以在生成后操作中部署应用程序。 可以向目录中的角色分配服务主体以限制 Jenkins 作业权限。 

对于开发和测试环境，可以通过配置 Azure 凭据或群集管理终结点来部署应用程序。 有关如何配置群集管理终结点的详细信息，请参阅[使用群集管理终结点配置部署](#configure-deployment-using-cluster-management-endpoint)。   

1. 若要创建 Azure Active Directory 服务主体并在 Azure 订阅中分配其权限，请按照[使用门户创建 Azure Active Directory 应用程序和服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)中的步骤操作。 请注意以下几点：

   * 按照本主题中的步骤操作时，请务必复制并保存以下值：应用程序 ID、应用程序密钥、目录 ID（租户 ID）和订阅 ID。 在 Jenkins 中配置 Azure 凭据时，需要这些值。
   * 如果目录上没有[所需权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)，则需要请求管理员授予权限或为你创建服务主体，否则，需要在 Jenkins 作业的“生成后操作”中配置群集的管理终结点。
   * 在[创建 Azure Active Directory 应用程序](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application)部分中，“登录 URL”可以输入任何格式标准的 URL。
   * 在[为应用程序分配角色](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role)部分中，可以在群集的资源组上为应用程序分配“读者”角色。

2. 在 Jenkins 作业中，单击“生成后操作”选项卡。
3. 在“生成后操作”下拉列表中，选择“部署 Service Fabric 项目”。 
4. 在“Service Fabric 群集配置”下，选择“选择 Service Fabric 群集”单选按钮。 单击“Azure 凭据”旁的“添加”。 单击“Jenkins”选择 Jenkins 凭据提供程序。
5. 在 Jenkins 凭据提供程序中，从“种类”下拉列表中选择“Microsoft Azure 服务主体”。
6. 使用在步骤 1 中设置服务主体时保存的值设置以下字段：

   * **客户端 ID**：（应用程序 ID）
   * **客户端密码**：应用程序密钥
   * **租户 ID**：目录 ID
   * **订阅 ID**：订阅 ID
6. 输入用来在 Jenkins 中选择凭据的描述性 ID 和一段简短说明。 然后，单击“验证服务主体”。 如果验证成功，单击“添加”。

   ![Service Fabric Jenkins 输入 Azure 凭据](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. 在“Service Fabric 群集配置”下，确定已为“Azure 凭据”选择新的凭据。 
8. 从“资源组”下拉列表，选择想要部署应用程序的群集的资源组。
9. 从“Service Fabric”下拉列表，选择想要部署应用程序的群集。
10. 对于“客户端密钥”和“客户端证书”，在 Jenkins 容器中输入 PEM 文件的位置。 例如，`/var/jenkins_home/clustercert.pem`。 
11. 在“应用程序配置”下，配置“应用程序名称”、“应用程序类型”和（相对）“应用程序清单路径”字段。
    ![Service Fabric Jenkins 生成后操作配置 Azure 凭据](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. 单击“验证配置”。 成功验证后，单击“保存”。 Jenkins 作业管道现在已完全配置。 转到[后续步骤](#next-steps)测试部署。

## <a name="troubleshooting-the-jenkins-plugin"></a>排查 Jenkins 插件问题

如果 Jenkins 插件出现任何 bug，请在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 中提出特定组件的问题。

## <a name="next-steps"></a>后续步骤
现已配置 GitHub 和 Jenkins。 考虑在存储库分支的 `reliable-services-actor-sample/Actors/ActorCounter` 项目中做出一些示例更改，https://github.com/Azure-Samples/service-fabric-java-getting-started。 将更改推送到远程 `master` 分支（或配置使用的任何分支）。 这会触发配置的 Jenkins 作业 `MyJob`。 它会从 GitHub 提取更改、生成这些更改并将应用程序部署到在生成后操作中指定的群集。  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

