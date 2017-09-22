---
title: "使用 Jenkins 插件部署到 Azure 应用服务 | Microsoft Docs"
description: "了解如何在 Jenkins 中使用 Azure 应用服务 Jenkins 插件将 Java Web 应用部署到 Azure"
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
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: bd4e629e522fb9acea5601be8eac7c70ae61d042
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>使用 Jenkins 插件部署到 Azure 应用服务 
若要将 Java Web 应用部署到 Azure，可在 [Jenkins 管道](/azure/jenkins/execute-cli-jenkins-pipeline)中使用 Azure CLI，也可使用 [Azure 应用服务 Jenkins 插件](https://plugins.jenkins.io/azure-app-service)。 本教程介绍如何：

> [!div class="checklist"]
> * 配置 Jenkins 以通过 FTP 部署到 Azure 应用服务 
> * 配置 Jenkins 以通过 Docker 部署到 Linux 版 Azure 应用服务 

## <a name="create-and-configure-jenkins-instance"></a>创建和配置 Jenkins 实例
如果还没有 Jenkins Master，请先使用[解决方案模板](install-jenkins-solution-template.md)，其中包含 JDK8 和以下必需插件：

* [Jenkins Git 客户端插件](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Docker Commons 插件](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Azure 凭据](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure 应用服务](https://plugins.jenkins.io/azure-app-server) v.0.1

应用服务插件可用于通过 Azure App Service 支持的所有语言（例如 C#、PHP、Java 和 node.js 等）部署 Web 应用。 本教程中使用示例 Java 应用 - [适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)。 要将存储库分叉到自己的 GitHub 帐户，请单击右上角的“分叉”按钮。  

Java JDK 和 Maven 为生成 Java 项目所必需。 若将其用于持续集成，请确保在 Jenkins Master 或 VM 代理中安装组件。 

若要安装，请使用 SSH 登录到 Jenkins 实例，并运行以下命令：

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

对于部署到 Linux 版应用服务，还需在 Jenkins Master 或 VM 代理上安装 Docker 用于生成。 请参阅下文安装 Docker：https://docs.docker.com/engine/installation/linux/ubuntu/。

## <a name="add-azure-service-principal-to-jenkins-credential"></a>将 Azure 服务主体添加到 Jenkins 凭据

部署到 Azure 需要 Azure 服务主体。 

<ol>
<li>使用 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 或 [Azure 门户](/azure/azure-resource-manager/resource-group-create-service-principal-portal)创建 Azure 服务主体</li>
<li>在 Jenkins 仪表板中，单击“Credentials”（凭据）->“System”（系统）。 单击“Global credentials(unrestricted)”（全局凭据(不受限制)）。</li>
<li>单击“添加凭据”，通过填写订阅 ID、客户端 ID、客户端密码和 OAuth 2.0 令牌终结点，添加 Microsoft Azure 服务主体。 提供 ID (mySp) 供后续步骤使用。</li>
</ol>

## <a name="azure-app-service-plugin"></a>Azure 应用服务插件

Azure 应用服务插件 v1.0 支持通过以下条件持续部署到 Azure Web 应用：

* Git 和 FTP
* 适用于 Linux 版 Web 应用的 Docker

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>配置 Jenkins 以使用 Jenkins 仪表板通过 FTP 部署 Web 应用

若要将项目部署到 Azure Web 应用，可以使用 Git 或 FTP 上传生成项目（例如，采用 Java 的 .war 文件）。

在 Jenkins 中设置作业前，需要 Azure 应用服务计划和 Web 应用，以便运行 Java 作业。


1. 使用 [az appservice plan create](/cli/azure/appservice/plan#create) CLI 命令通过“免费”定价层创建 Azure 应用服务计划。 appservice 计划定义用于托管应用的物理资源。 分配到 appservice 计划的所有应用程序共享这些资源，因此在托管多个应用时可以节省成本。
2. 创建 Web 应用。 可以使用 [Azure 门户](/azure/app-service/web-sites-configure)或以下 Az CLI 命令：
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. 请确保设置应用所需的 Java 运行时配置。 以下 Azure CLI 命令配置的 Web 应用可在最新的 Java 8 JDK 和 [Apache Tomcat](http://tomcat.apache.org/) 8.0 上运行。
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>设置 Jenkins 作业


1. 在 Jenkins 仪表板中创建新的自由式项目
2. 通过提供存储库 URL 来配置源代码管理，以使用[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)的本地分支。 例如：http://github.com/&lt;yourID>/javawebappsample。
3. 添加生成步骤，使用 Maven 生成项目。 通过添加执行 shell 实现该操作。 此示例中我们需要执行其他步骤，将目标文件夹中的 *.war 文件重命名为 ROOT.war。   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. 选择“发布 Azure Web 应用”来添加生成后操作。
5. 提供上一步中存储的 Azure 服务主体“mySp”。
6. 在“应用配置”部分，选择订阅中的资源组和 Web 应用。 插件自动检测 Web 应用基于 Windows 还是基于 Linux。 对于基于 Windows 的 Web 应用，存在“发布文件”的选项。
7. 填写要部署的文件（例如，如果使用的是 Java，则填写 .war 包）。源目录和目标目录是可选的。 参数允许在上传文件时指定源文件夹和目标文件夹。 Azure 上的 Java Web 应用在 Tomcat 服务器中运行。 因此，将 war 包上传到 webapps 文件夹。 对于此示例，将“源目录”设为“target”并将“webapps”用作“目标目录”。
8. 如果想要部署到非生产槽，还可设置槽名称。
9. 保存并生成项目。 生成完成后，Web 应用将部署到 Azure。

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>使用 Jenkins 管道通过 FTP 部署 Web 应用

插件中管道已就绪。 可以参考 GitHub 存储库中的示例。

1. 在 GitHub Web UI 中打开 Jenkinsfile_ftp_plugin 文件。 单击铅笔图标编辑此文件，分别更新第 11 行和第 12 行上的资源组和 Web 应用名称。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 更改第 14 行，更新 Jenkins 实例中的凭据 ID。    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>创建 Jenkins 管道

1. 在 Web 浏览器中打开 Jenkins，单击“New Item”（新建项）。
2. 为该作业提供一个名称并选择“Pipeline”（管道）。 单击 **“确定”**。
3. 接下来单击“Pipeline”（管道）选项卡。
4. 对于“Definition”（定义），选择“Pipeline script from SCM”（来自 SCM 的管道脚本）。
5. 对于“SCM”，选择“Git”。 输入分叉存储库的 GitHub URL：https:&lt;your forked repo>.git
6. 将脚本路径更新为“Jenkinsfile_ftp_plugin”
7. 单击“保存”并运行作业。

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>配置 Jenkins 以通过 Docker 部署到 Linux 版 Web 应用

除了 Git/FTP，Linux 版 Web 应用还支持使用 Docker 进行部署。 使用 Docker 进行部署需提供 Dockerfile，以便使用服务运行时将 Web 应用打包到 Docker 映像。 插件随后生成映像，将其推送到 Docker 注册表，并将映像部署到 Web 应用。

Linux 版 Web 应用还支持 Git 和 FTP 等传统方式，但仅限内置语言（.NET Core、Node.js、PHP 和 Ruby）。 对于其他语言，需将应用程序代码和服务运行时一并打包到 Docker 映像并使用 Docker 进行部署。

在 Jenkins 中设置作业前，首先需要 Linux 版 Azure App Service。 还需容器注册表来存储与管理专用 Docker 容器映像。 可以使用 DockerHub，而此示例使用 Azure 容器注册表。

* 可按照[此处](../app-service/containers/quickstart-nodejs.md)的步骤进行操作，创建 Linux 版 Web 应用 
* Azure 容器注册表是基于开源 Docker 注册表 2.0 的托管 [Docker 注册表] (https://docs.docker.com/registry/) 服务。 有关如何执行此操作的更多指导，请按照 [此处] (/azure/container-registry/container-registry-get-started-azure-cli) 的步骤进行操作。 还可使用 DockerHub。

### <a name="to-deploy-using-docker"></a>使用 Docker 进行部署：

1. 在 Jenkins 仪表板中创建新的自由式项目。
2. 通过提供存储库 URL 来配置源代码管理，以使用[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)的本地分支。 例如：http://github.com/&lt;yourid>/javawebappsample。
添加生成步骤，使用 Maven 生成项目。 通过添加执行 shell 并在“命令”中添加以下行实现此操作：    
```bash
mvn clean package
```

3. 选择“发布 Azure Web 应用”来添加生成后操作。
4. 提供上一步中存储的 Azure 服务主体“mySp”作为 Azure 凭据。
5. 在“应用配置”部分，选择订阅中的资源组和 Linux Web 应用。
6. 选择“通过 Docker 发布”。
7. 填写 Dockerfile 路径。 可以保留默认“/Dockerfile”。如果使用 Azure 容器注册表，对于 Docker 注册表 URL，请以 https://&lt;myRegistry>.azurecr.io 格式提供。 如果使用 DockerHub 则留空。
8. 对于“注册表凭据”，请添加 Azure 容器注册表的凭据。 可通过在 Azure CLI 中运行以下命令来获取用户 ID 和密码。 第一个命令启用管理员帐户。    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. “高级”选项卡中的 Docker 映像名称和标记是可选的。 默认情况下，映像名称即为 Azure 门户中配置的映像名称（在 Docker 容器设置中）。标志从 $BUILD_NUMBER 生成。 请确保在 Azure 门户中指定映像名称，或在“高级”选项卡中为 Docker 映像提供一个值。对于此示例，请将“&lt;yourRegistry>.azurecr.io/calculator”作为 Docker 映像并将“Docker 映像标记”留空。
10. 请注意，如果使用内置 Docker 映像设置，则部署将失败。 请确保更改 Docker 配置，以在 Azure 门户的 Docker 容器设置中使用自定义映像。 对于内置映像，请使用文件上传方法进行部署。
11. 与文件上传方法类似，可以选择其他非生产槽。
12. 保存并生成项目。 可以发现容器映像已推送到注册表，且 Web 应用已部署。

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>使用 Jenkins 管道通过 Docker 部署到 Linux 版 Web 应用

1. 在 GitHub Web UI 中打开 Jenkinsfile_container_plugin 文件。 单击铅笔图标编辑此文件，分别更新第 11 行和第 12 行上的资源组和 Web 应用名称。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 将第 13 行更改为容器注册表服务器    
```java
def registryServer = '<registryURL>'
```    

3. 更改第 16 行，更新 Jenkins 实例中的凭据 ID    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>创建 Jenkins 管道    

1. 在 Web 浏览器中打开 Jenkins，单击“New Item”（新建项）。
2. 为该作业提供一个名称并选择“Pipeline”（管道）。 单击 **“确定”**。
3. 接下来单击“Pipeline”（管道）选项卡。
4. 对于“Definition”（定义），选择“Pipeline script from SCM”（来自 SCM 的管道脚本）。
5. 对于“SCM”，选择“Git”。
6. 输入分叉存储库的 GitHub URL：https:&lt;your forked repo>.git</li>
7. 将脚本路径更新为“Jenkinsfile_container_plugin”
8. 单击“保存”并运行作业。

## <a name="verify-your-web-app"></a>验证 Web 应用

1. 确保 WAR 文件已成功部署到 Web 应用。 打开 Web 浏览器。
2. 转到 http://&lt;app_name>.azurewebsites.net/api/calculator/ping，你将发现：    
     欢迎使用 Java Web 应用!!! 已更新!
   Sun Jun 17 16:39:10 UTC 2017
3. 转到 http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>（将 &lt;x> 和 &lt;y> 替换为任意数字）获取 x 与 y 的和        
    ![计算器：加](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>对于 Linux 版应用服务

* 若要进行验证，请在 Azure CLI 中运行：

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    将得到以下结果：
    
    ```
    [
    "calculator"
    ]
    ```
    
    转到 http://&lt;app_name>.azurewebsites.net/api/calculator/ping。 将显示以下消息： 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    转到 http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>（将 &lt;x> 和 &lt;y> 替换为任意数字）获取 x 与 y 的和
    
## <a name="next-steps"></a>后续步骤

本教程中使用 Azure 应用服务插件部署到 Azure。

你已了解如何：

> [!div class="checklist"]
> * 配置 Jenkins 以通过 FTP 部署 Azure 应用服务 
> * 配置 Jenkins 以通过 Docker 部署到 Linux 版 Azure 应用服务 
