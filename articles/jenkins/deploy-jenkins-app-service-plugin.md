---
title: "使用 Jenkins 插件部署到 Azure App Service | Microsoft Docs"
description: "了解如何在 Jenkins 中使用 Azure App Service Jenkins 插件将 Java Web 应用部署到 Azure"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 778fe746f1e8dff1d1c80b6ba7d8f10cc2bfacee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>使用 Jenkins 插件部署到 Azure App Service 
要将 Java Web 应用部署到 Azure，可在 [Jenkins 管道](/azure/jenkins/execute-cli-jenkins-pipeline)中使用 Azure CLI，也可使用 [Azure App Service Jenkins 插件](https://plugins.jenkins.io/azure-app-service)。 1.0 版 Jenkins 插件支持通过以下方式使用 Azure App Service 的 Web 应用功能进行持续部署：
* Git 和 FTP。
* 适用于 Linux 版 Web 应用的 Docker。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 配置 Jenkins 以通过 Git 和 FTP 部署 Web 应用。 
> * 配置 Jenkins 以部署适用于容器的 Web 应用。 


## <a name="create-and-configure-a-jenkins-instance"></a>创建和配置 Jenkins 实例
如果还没有 Jenkins Master，请先使用[解决方案模板](install-jenkins-solution-template.md)，其中包含 Java Development Kit (JDK) 版本 8 和以下必需 Jenkins 插件：

* [Jenkins Git 客户端插件](https://plugins.jenkins.io/git-client)版本 .2.4.6 
* [Docker Commons 插件](https://plugins.jenkins.io/docker-commons)版本.1.4.0
* [Azure 凭据](https://plugins.jenkins.io/azure-credentials)版本 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) 版本 0.1

Jenkins 插件可用于通过 Web 应用支持的所有语言（例如 C#、PHP、Java 和 Node.js）部署 Web 应用。 本教程使用[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)。 要将存储库分叉到自己的 GitHub 帐户，请选择 GitHub 界面右上角的“分叉”按钮。  
> [!NOTE]
> Java JDK 和 Maven 为生成 Java 项目所必需。 在 Jenkins Master 上安装这些组件，或若将代理用于持续集成，则在 VM 代理中安装组件。 

要安装组件，请使用 SSH 登录到 Jenkins 实例，并运行以下命令：

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

要部署到适用于容器的 Web 应用，请在 Jenkins Master 或 VM 代理上安装 Docker 用于生成。 如需说明，请参阅[在 Ubuntu 上安装 Docker](https://docs.docker.com/engine/installation/linux/ubuntu/)。

##<a name="service-principal"></a>将 Azure 服务主体添加到 Jenkins 凭据

部署到 Azure 需要 Azure 服务主体。 


1. 要创建 Azure 服务主体，请使用 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 或 [Azure 门户](/azure/azure-resource-manager/resource-group-create-service-principal-portal)。
2. 在 Jenkins 仪表板中，选择“凭据” > “系统”。 然后选择“全局凭据(不受限制)”。
3. 要添加 Microsoft Azure 服务主体，请选择“添加凭据”。 为“订阅 ID”、“客户端 ID”、“客户端密码”和“OAuth 2.0 令牌终结点”字段提供值。 将“ID”字段设为“mySp”。 本文后续步骤中将使用此 ID。


## <a name="configure-jenkins-to-deploy-web-apps-through-git-and-ftp"></a>配置 Jenkins 以通过 Git 和 FTP 部署 Web 应用

要将项目部署到 Web 应用，可使用 Git 或 FTP 上传生成项目（例如，采用 Java 的 WAR 文件）。

在 Jenkins 中设置作业前，需要 Azure 应用服务计划和 Web 应用以运行 Java 应用。


1. 使用 `az appservice plan create` [Azure CLI 命令](/cli/azure/appservice/plan#create)通过免费定价层创建 Azure 应用服务计划。 应用服务计划定义用于托管应用的物理资源。 分配到应用服务计划的所有应用程序共享这些资源。 共享资源可帮助你在托管多个应用时节省成本。
2. 创建 Web 应用。 可使用 [Azure 门户](/azure/app-service-web/web-sites-configure)或以下 `az` Azure CLI 命令：
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. 设置应用所需的 Java 运行时配置。 以下 Azure CLI 命令配置的 Web 应用可在最新的 JDK 8 和 [Apache Tomcat](http://tomcat.apache.org/) 版本 8.0 上运行：
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>设置 Jenkins 作业

1. 在 Jenkins 仪表板上创建新的自由式项目。
2. 配置“源代码管理”字段，以使用[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)的本地分支。 提供“存储库 URL”值。 例如：http://github.com/&lt;your_ID>/javawebappsample。
3. 添加步骤以使用 Maven 生成项目，可通过添加 Execute shell 命令实现该操作。 在此示例中，需使用其他命令将目标文件夹中的 \*.war 文件重命名为 ROOT.war：   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. 选择“发布 Azure Web 应用”来添加生成后操作。
5. 将 mySp 作为 Azure 服务主体。 此主体在上一步中存储为 [Azure 凭据](#service-principal)。
6. 在“应用配置”部分，选择订阅中的资源组和 Web 应用。 Jenkins 插件自动检测 Web 应用是基于 Windows 还是基于 Linux。 对于 Windows Web 应用，存在“发布文件”选项。
7. 填写要部署的文件。 例如，如果使用 Java，则指定 WAR 包。 使用可选的“源目录”和“目标目录”参数，指定要用于文件上传的源文件夹和目标文件夹。 Azure 上的 Java Web 应用在 Tomcat 服务器中运行。 因此对于 Java，请将 WAR 包上传到 webapps 文件夹。 对于此示例，请将“源目录”值设为“target”，将“目标目录”值设为“webapps”。
8. 若要部署到非生产槽，还可设置槽名称。
9. 保存并生成项目。 生成完成后，Web 应用将部署到 Azure。

### <a name="deploy-web-apps-through-ftp-by-using-jenkins-pipeline"></a>使用 Jenkins 管道通过 FTP 部署 Web 应用

Azure App Service Jenkins 插件中管道已就绪。 可参考 GitHub 存储库中的以下示例。

1. 在 GitHub 界面中打开 Jenkinsfile_ftp_plugin 文件。 要编辑该文件，请选择铅笔图标。 在第 11 行和第 12 行上分别更新 Web 应用的 resourceGroup 和 webAppName 定义：
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 将第 14 行上的 withCredentials 定义设置为 Jenkins 实例中的凭据 ID：
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>创建 Jenkins 管道

1. 在 Web 浏览器中打开 Jenkins。 选择“新建项”。
2. 为该作业提供一个名称并选择“Pipeline”（管道）。 选择“确定”。
3. 选择“管道”选项卡。
4. 对于“定义”值，请选择“来自 SCM 的管道脚本”。
5. 对于“SCM”值，请选择“Git”。 输入分叉存储库的 GitHub URL。 例如：https://&lt;your_forked_repo>.git。
6. 将“脚本路径”值更新为“Jenkinsfile_ftp_plugin”。
7. 选择“保存”并运行作业。

## <a name="configure-jenkins-to-deploy-web-apps-for-containers"></a>配置 Jenkins 以部署适用于容器的 Web 应用

Linux 版 Web 应用支持使用 Docker 进行部署。 要使用 Docker 部署 Web 应用，需提供 Dockerfile，以便使用服务运行时将 Web 应用打包到 Docker 映像。 随后，Jenkins 插件生成映像、将其推送到 Docker 注册表，并将映像部署到 Web 应用。

Linux 版 Web 应用还支持 Git 和 FTP 等传统部署方法，但仅限内置语言（.NET Core、Node.js、PHP 和 Ruby）。 对于其他语言，需将应用程序代码和服务运行时一并打包到 Docker 映像并使用 Docker 进行部署。

在 Jenkins 中设置作业前，首先需要 Linux 版 Web 应用。 还需容器注册表来存储和管理专用 Docker 容器映像。 可使用 DockerHub 创建容器注册表。 本示例使用 Azure 容器注册表。

* [在 Linux 上创建 Web 应用](../app-service/containers/quickstart-nodejs.md)。
* Azure 容器注册表是基于开源 Docker 注册表版本 2.0 的托管 [Docker 注册表](https://docs.docker.com/registry/)服务。 [创建 Azure 容器注册表](/azure/container-registry/container-registry-get-started-azure-cli)。 还可使用 DockerHub。

### <a name="set-up-the-jenkins-job-for-docker"></a>设置适用于 Docker 的 Jenkins 作业

1. 在 Jenkins 仪表板上创建新的自由式项目。
2. 配置“源代码管理”字段，以使用[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)的本地分支。 提供“存储库 URL”值。 例如：http://github.com/&lt;your_ID>/javawebappsample。
3. 添加步骤以使用 Maven 生成项目，可通过添加 Execute shell 命令实现该操作。 在命令中包括以下行：
    ```bash
    mvn clean package
    ```

4. 选择“发布 Azure Web 应用”来添加生成后操作。
5. 将 mySp 作为 Azure 服务主体。 此主体在上一步中存储为 [Azure 凭据](#service-principal)。
6. 在“应用配置”部分，选择订阅中的资源组和 Linux Web 应用。
7. 选择“通过 Docker 发布”。
8. 填写“Dockerfile”路径值。 可保留默认值/Dockerfile。
如果使用 Azure 容器注册表，那么对于“Docker 注册表 URL”值，请使用 https://&lt;yourRegistry>.azurecr.io 格式提供 URL。 如果使用 DockerHub，请将该值留空。
9. 对于“注册表凭据”值，请添加容器注册表的凭据。 可通过在 Azure CLI 中运行以下命令来获取用户 ID 和密码。 第一个命令启用管理员帐户：
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. “高级”选项卡中的 Docker 映像名称和标记值是可选的。 默认情况下，映像名称的值即为在 Azure 门户的“Docker 容器”设置中配置的映像名称。 标志从 $BUILD_NUMBER 生成。
    > [!NOTE]
    > 请确保在 Azure 门户中指定映像名称，或在“高级”选项卡中提供“Docker 映像”值。对于此示例，请将“Docker 映像”值设置为 &lt;your_Registry>.azurecr.io/calculator 并将“Docker 映像标记”值留空。

11. 如果使用内置 Docker 映像设置，则部署将失败。 更改 Docker 配置，以在 Azure 门户的“Docker 容器”设置中使用自定义映像。 对于内置映像，请使用文件上传方法进行部署。
12. 与文件上传方法类似，可以选择其他非生产槽名称。
13. 保存并生成项目。 容器映像已推送到注册表，且 Web 应用已部署。

### <a name="deploy-web-apps-for-containers-by-using-jenkins-pipeline"></a>使用 Jenkins 管道部署适用于容器的 Web 应用

1. 在 GitHub 界面中打开 Jenkinsfile_container_plugin 文件。 要编辑该文件，请选择铅笔图标。 在第 11 行和第 12 行上分别更新 Web 应用的 resourceGroup 和 webAppName 定义：
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 将第 13 行更改为容器注册表服务器：   
    ```java
    def registryServer = '<registryURL>'
    ```    

3. 更改第 16 行，以在 Jenkins 实例中使用凭据 ID：  
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```    

### <a name="create-a-jenkins-pipeline"></a>创建 Jenkins 管道    

1. 在 Web 浏览器中打开 Jenkins。 选择“新建项”。
2. 为该作业提供一个名称并选择“Pipeline”（管道）。 选择“确定”。
3. 选择“管道”选项卡。
4. 对于“定义”值，请选择“来自 SCM 的管道脚本”。
5. 对于“SCM”值，请选择“Git”。 输入分叉存储库的 GitHub URL。 例如：https://&lt;your_forked_repo>.git。
7. 将“脚本路径”值更新为“Jenkinsfile_container_plugin”。
8. 选择“保存”并运行作业。

## <a name="verify-your-web-app"></a>验证 Web 应用

1. 要确保 WAR 文件已成功部署到 Web 应用，请打开 Web 浏览器。
2. 转到 http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping。 将 &lt;your_app_name> 替换为 Web 应用名称。 将显示以下消息：
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. 转到 http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>。 将 &lt;x> 和 &lt;y> 替换为任意数字，得出 x + y 的和。 计算器显示总和：![计算器：相加](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>对于 Linux 版 Azure App Service

1. 要验证 Web 应用，请在 Azure CLI 中运行以下命令：
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    将显示以下消息：
    ```CLI
    ["calculator"]
    ```
    
2. 转到 http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping。 将 &lt;your_app_name> 替换为 Web 应用名称。 将显示以下消息： 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. 转到 http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>。 将 &lt;x> 和 &lt;y> 替换为任意数字，得出 x + y 的和。
    
## <a name="next-steps"></a>后续步骤

本教程使用 Azure App Service Jenkins 插件部署到 Azure。

你已了解如何：

> [!div class="checklist"]
> * 配置 Jenkins 以通过 FTP 部署 Azure 应用服务 
> * 配置 Jenkins 以部署到适用于容器的 Web 应用 