---
title: 设置 Azure Service Fabric Java 应用程序的 CI/CD | Microsoft Docs
description: 本教程介绍如何设置使用 Jenkins 部署 Java Service Fabric 应用程序的持续集成。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: bd986b8741b55a10018f7400c9415e7aedfbf119
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
ms.locfileid: "29949831"
---
# <a name="tutorial-set-up-a-jenkins-environment-with-service-fabric"></a>教程：使用 Service Fabric 设置 Jenkins 环境
本教程是系列教程的第五部分， 介绍如何使用 Jenkins 将升级部署到应用程序。 本教程结合使用 Service Fabric Jenkins 插件和托管投票应用程序的 Github 存储库，将应用程序部署到群集。 

本系列教程的第五部分介绍以下操作：
> [!div class="checklist"]
> * 在计算机上部署 Service Fabric Jenkins 容器
> * 设置要部署到 Service Fabric 的 Jenkins 环境
> * 升级应用程序

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [生成 Java Service Fabric Reliable Services 应用程序](service-fabric-tutorial-create-java-app.md)
> * [在本地群集上部署和调试应用程序](service-fabric-tutorial-debug-log-local-cluster.md)
> * [将应用程序部署到 Azure 群集](service-fabric-tutorial-java-deploy-azure.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-java-elk.md)
> * 设置 CI/CD


## <a name="prerequisites"></a>先决条件
- 通过 [Git 下载页](https://git-scm.com/downloads)在本地计算机上安装 Git。 有关 Git 的详细信息，请参阅 [Git 文档](https://git-scm.com/docs)。
- 在 [Jenkins](https://jenkins.io/) 方面有实践经验。
- 创建 [GitHub](https://github.com/) 帐户并知道如何使用 GitHub。
- 在计算机上安装 [Docker](https://www.docker.com/community-edition)。

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>提取并部署 Service Fabric Jenkins 容器映像
可在 Service Fabric 群集内部或外部设置 Jenkins。 以下说明介绍如何使用提供的 Docker 映像在群集外部设置 Jenkins。 但也可以使用预配置的 Jenkins 生成环境。 以下容器映像已连同 Service Fabric 插件一起安装，随时可与 Service Fabric 配合使用。 

1. 拉取 Service Fabric Jenkins 容器映像：``docker pull rapatchi/jenkins:v10``。 此映像附带了预安装的 Service Fabric Jenkins 插件。

2. 结合证书在本地计算机上的装载位置参数运行容器映像

    ```bash
    docker run -itd -p 8080:8080 -v /Users/suhuruli/Documents/Work/Samples/service-fabric-java-quickstart/AzureCluster:/tmp/myCerts rapatchi/jenkins:v10
    ```

3. 获取容器映像实例的 ID。 可以使用命令 ``docker ps –a`` 列出所有 Docker 容器

4. 运行以下命令，检索 Jenkins 实例的密码：

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    如果容器 ID 为 2d24a73b5964，请使用 2d24。
    * 从门户 ``http://<HOST-IP>:8080`` 登录到 Jenkins 仪表板时需要此密码
    * 首次登录后，可以创建自己的用户帐户或使用管理员帐户。
    
5. 使用[生成新的 SSH 密钥并将其添加到 SSH 代理](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)中所述的步骤，将 GitHub 设置为使用 Jenkins。 由于命令是从 Docker 容器运行的，因此请遵照适用于 Linux 环境的说明。 
    * 使用 GitHub 提供的说明生成 SSH 密钥。 接下来，将 SSH 密钥添加到托管存储库的 GitHub 帐户。
    * 在 Jenkins Docker shell（而不是主机）中运行上述链接中提到的命令。
    * 若要从主机登录到 Jenkins shell，请使用以下命令：

    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

    确保托管 Jenkins 容器映像的群集或计算机使用公共 IP。 使用公共 IP 可让 Jenkins 实例从 GitHub 接收通知。    

## <a name="create-and-configure-a-jenkins-job"></a>创建和配置 Jenkins 作业

1. 首先，如果没有可用于在 Github 上托管投票项目的存储库，请创建一个存储库。 在本教程的余下内容中，此存储库名为 **dev_test**。

2. 在 Jenkins 仪表板上创建一个**新项**。

3. 输入项名称（例如 **MyJob**）。 选择“自由格式的项目”，并单击“确定”。

4. 转到作业页，单击“配置”。

   a. 在常规部分中，选择“GitHub 项目”所对应的复选框，指定 GitHub 项目 URL。 此 URL 托管要与 Jenkins 持续集成和持续部署 (CI/CD) 流（例如 ``https://github.com/testaccount/dev_test``）集成的 Service Fabric Java 应用程序。

   b. 在“源代码管理”部分，选择 **Git**。 指定用于托管要与 Jenkins CI/CD 流（例如 *https://github.com/testaccount/dev_test.git*）集成的 Service Fabric Java 应用程序的存储库 URL。 也可在此处指定要生成的分支（例如 **/master**）。

5. 配置 *GitHub*（存储库的托管位置），使它能够与 Jenkins 通信。 请执行以下步骤：

   a. 转到 GitHub 存储库页。 转到“设置” > “集成和服务”。

   b. 选择“添加服务”，键入 **Jenkins**，并选择“Jenkins-GitHub 插件”。

   c. 输入 Jenkins Webhook URL（默认为 ``http://<PublicIPorFQDN>:8081/github-webhook/``）。 单击“添加/更新服务”。

   d.单击“下一步”。 将向 Jenkins 实例发送一个测试事件。 GitHub 中的 Webhook 旁边应会显示一个绿色复选标记，同时会生成项目。

   ![Service Fabric Jenkins 配置](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

6. 在“生成触发器”部分下面，选择所需的生成选项。 在此示例中，我们希望每当向存储库推送信息，就会触发生成。 为此，可以选择“用于 GITScm 轮询的 GitHub 挂钩触发器”。

7. 在“生成”部分下面，从“添加生成步骤”下拉列表中选择“调用 Gradle 脚本”。 在出现的小组件中，打开高级菜单，为应用程序指定“根生成脚本”的路径。 该脚本将从指定的路径中选择 build.gradle，然后执行相应的操作。

    ![Service Fabric Jenkins 生成操作](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)
  
8. 在“生成后操作”下拉列表中，选择“部署 Service Fabric 项目”。 此处需要提供有关在何处部署 Jenkins 编译的 Service Fabric 应用程序的群集详细信息。 证书的路径是卷的装载位置 (/tmp/myCerts)。 
   
    还可以提供用于部署应用程序的其他详细信息。 有关应用程序详细信息的示例，请参阅以下屏幕截图：

    ![Service Fabric Jenkins 生成操作](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > 如果使用 Service Fabric 部署 Jenkins 容器映像，此处的群集可与托管 Jenkins 容器应用程序的群集相同。
    >

## <a name="update-your-existing-application"></a>更新现有应用程序 

1. 使用 **Service Fabric 投票示例 V2** 更新 *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* 文件中 HTML 的标题。 

    ```html 
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

2. 在 *Voting/VotingApplication/ApplicationManifest.xml* 文件中，将 **ApplicationTypeVersion** 和 **ServiceManifestVersion** 版本更新为 **2.0.0**。 

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>      
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>      
    </ApplicationManifest>
    ```

3. 在 *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* 文件中，将 **ServiceManifest** 中的 **Version** 字段以及 **CodePackage** 标记中的 **Version** 字段更新为 **2.0.0**。

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

4. 若要初始化执行应用程序升级的 Jenkins 作业，请将新更改推送到 Github 存储库。 

5. 在 Service Fabric Explorer 中，单击“应用程序”下拉列表。 若要查看升级状态，请单击“正在进行升级”选项卡。

    ![正在进行升级](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

6. 如果访问 **http://\<主机 IP >:8080**，会看到具有完整功能的投票应用程序正在运行。 

    ![本地投票应用](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 在计算机上部署 Service Fabric Jenkins 容器
> * 设置要部署到 Service Fabric 的 Jenkins 环境
> * 升级应用程序

* 查看其他 [Java 示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)