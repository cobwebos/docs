---
title: 使用 Jenkins 将 Web 应用部署到 Azure | Microsoft Docs
description: 使用 Jenkins 和 Docker 设置 Java Web 应用从 GitHub 到 Azure 应用服务的持续集成。
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: ''
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852993"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>使用 Jenkins 设置 Azure 应用服务的持续集成和部署

本教程使用 Jenkins 设置通过 [Spring Boot](http://projects.spring.io/spring-boot/) 框架开发的示例 Java Web 应用的持续集成和部署 (CI/CD)，并推送到 [Linux 上的 Azure 应用服务 Web 应用](/azure/app-service/containers/app-service-linux-intro)。

在本教程中，将执行以下任务：

> [!div class="checklist"]
> * 安装部署到 Azure 应用服务所需的 Jenkins 插件。
> * 推送新提交时，定义 Jenkins 作业以便从 GitHub 存储库生成 Docker 映像。
> * 定义适用于 Linux 的新 Azure Web 应用，并对其进行配置以便将推送的 Docker 映像部署到 Azure 容器注册表。
> * 配置 Azure 应用服务 Jenkins 插件。
> * 使用手动生成将示例应用部署到 Azure 应用服务。
> * 触发 Jenkins 生成并通过将更改推送到 GitHub 更新 Web 应用。

## <a name="before-you-begin"></a>开始之前

要完成本教程，需要：

* 使用 JDK 和 Maven 工具配置的 [Jenkins](https://jenkins.io/)。 如果没有 Jenkins 系统，请立即在 Azure 中使用 [Jenkins 解决方案模板](/azure/jenkins/install-jenkins-solution-template)创建一个。
* 一个 [GitHub](https://github.com) 帐户。
* [Azure CLI 2.0](/cli/azure)（来自本地命令行或位于 [Azure Cloud Shell](/azure/cloud-shell/overview) 中）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>安装 Jenkins 插件

1. 打开 Web 浏览器转到 Jenkins Web 控制台，从左侧菜单中选择“管理 Jenkins”，然后选择“管理插件”。
2. 选择“可用”选项卡。
3. 搜索并选中以下插件旁边的复选框：   

    - [Azure 应用服务插件](https://plugins.jenkins.io/azure-app-service)
    - [GitHub Branch Source 插件](https://plugins.jenkins.io/github-branch-source)

    如果未出现插件，请选中“已安装”选项卡确保它们尚未安装。

1. 选择“立即下载并在重启后安装”，以启用 Jenkins 配置中的插件。

## <a name="configure-github-and-jenkins"></a>配置 GitHub 和 Jenkins

推送新提交到帐户中的存储库时，请设置 Jenkins 以接收 [GitHub webhook](https://developer.github.com/webhooks/)。

1. 选择“管理 Jenkins”，然后选择“配置系统”。 在“GitHub”部分中，请确保已选择“管理挂钩”，然后选择“管理其他 GitHub 操作”，并选择“将登录名和密码转换为令牌”。
2. 选择“使用登录名和密码”单选按钮并输入 GitHub 用户名和密码。 选择“创建令牌凭据”创建新的 [GitHub 个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。   
   ![使用登录名和密码创建 GitHub PAT](media/jenkins-java-quickstart/create_github_credentials.png)
3.  从 GitHub 服务器配置中的“凭据”下拉列表中选择新创建的令牌。 选择“测试连接”以验证身份验证是否正常工作。   
   ![配置 PAT 后验证到 GitHub 的连接](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> 如果 GitHub 帐户已启用双重身份验证，请在 GitHub 上创建令牌，并配置 Jenkins 使用该令牌。 有关完整详细信息，请参阅 [Jenkins GitHub 插件](https://wiki.jenkins.io/display/JENKINS/Github+Plugin)文档。

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>创建示例存储库分支和 Jenkins 作业 

1. 打开 [Spring Boot 示例应用程序存储库](https://github.com/spring-guides/gs-spring-boot-docker)，通过选择右上角的“创建分支”为自己的 GitHub 帐户创建分支。   
    ![从 GitHub 创建分支](media/jenkins-java-quickstart/fork_github_repo.png)
1. 在 Jenkins Web 控制台中，选择“新建项”，将其命名为“MyJavaApp”，选择“自由风格项目”，然后选择“确定”。   
    ![新的 Jenkins 自由风格项目](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. 在“常规”部分下面，选择“GitHub”项目并输入分叉的存储库的 URL，例如 https://github.com/raisa/gs-spring-boot-docker
3. 在“源代码管理”部分下面，选择“Git”并输入分叉的存储库 `.git` 的 URL，例如 https://github.com/raisa/gs-spring-boot-docker.git
4. 在“生成触发器”部分下面，选择“用于 GITscm 轮询的 GitHub 挂钩触发器”。
5. 在“生成”部分下面，选择“添加生成步骤”，并选择“调用顶级 Maven 目标”。 在“目标”字段中输入 `package`。
6. 选择“保存”。 可以通过从项目页选择“立即生成”测试作业。

## <a name="configure-azure-app-service"></a>配置 Azure 应用服务 

1. 使用 Azure CLI 或 [Cloud Shell](/azure/cloud-shell/overview)，创建新的 [Linux 上的 Web 应用](/azure/app-service/containers/app-service-linux-intro)。 本教程中的 Web 应用名称为 `myJavaApp`，但自己的应用需要使用唯一名称。
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. 创建 [Azure 容器注册表](/azure/container-registry/container-registry-intro)以存储 Jenkins 生成的 Docker 映像。 本教程中使用的容器注册表名称为 `jenkinsregistry`，但自己的容器注册表需要使用唯一名称。 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. 配置 Web 应用以运行推送到容器注册表中的 Docker 映像，并指定容器中运行的应用侦听端口 8080 上的请求。   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>配置 Azure 应用服务 Jenkins 插件

1. 在 Jenkins Web 控制台中，选择创建的“MyJavaApp”作业，然后选择页面左侧的“配置”。
2. 向下滚动到“生成后操作”，选择“添加生成后操作”，然后选择“发布 Azure Web 应用”。
3. 在“Azure 配置文件配置”下，选择“Azure 凭据”旁边的“添加”，并选择“Jenkins”。
4. 在“添加凭据”对话框中，从“种类”下拉列表中选择“Microsoft Azure 服务主体”。
5. 使用 Azure CLI 或 [Cloud Shell](/azure/cloud-shell/overview) 创建 Active Directory 服务主体。
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. 将服务主体的凭据输入到“添加凭据”对话框。 如果不知道 Azure 订阅 ID，则可以从 CLI 中查询：
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![配置 Azure 服务主体](media/jenkins-java-quickstart/azure_service_principal.png)
6. 通过选择“验证服务主体”验证使用 Azure 进行身份验证的服务主体。 
7. 选择“添加”以保存凭据。
8. 回到“发布 Azure Web 应用”配置时，请选择刚刚从“Azure 凭据”列表添加的服务主体凭据。
9. 在“应用配置”中，从下拉列表中选择资源组和 Web 应用名称。
10. 选择“通过 Docker 发布”单选按钮。
11. 针对 Dockerfile 路径输入 `complete/Dockerfile`。
12. 在“Docker 注册表 URL”字段中输入 `https://jenkinsregistry.azurecr.io`。
13. 选择“注册表凭据”旁边的“添加”。 
14. 为创建的 Azure 容器注册表输入管理员用户名作为用户名。
15. 在“密码”字段中，输入 Azure 容器注册表的密码。 可以从 Azure 门户或通过以下 CLI 命令获取用户名和密码：

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![添加容器注册表凭据](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. 选择“添加”以保存凭据。
16. 在“发布 Azure Web 应用”的“应用配置”面板中，从“注册表凭据”下拉列表选择新创建的凭据。 完成的生成后操作应如下图所示：   
    ![用于 Azure 应用服务部署的生成后操作配置](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. 选择“保存”保存作业配置。

## <a name="deploy-the-app-from-github"></a>使用 GitHub 部署应用

1. 从 Jenkins 项目中，选择“立即生成”，将示例应用部署到 Azure。
2. 生成完成后，应用将显示在 Azure 的发布 URL（如 http://myjavaapp.azurewebsites.net）上。   
   ![查看 Azure 上已部署的应用](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>推送更改并重新部署

1. 从 Github 分支，浏览 Web 转至 `complete/src/main/java/Hello/Application.java`。 选择 GitHub 接口右侧的“编辑此文件”链接。
2. 对 `home()` 方法进行以下更改并将更改提交到存储库的主分支。
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Jenkins 中会开始新的生成，由存储库的 `master` 分支上的新提交触发。 完成后，请在 Azure 上重新加载应用。     
      ![查看 Azure 上已部署的应用](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>后续步骤

- [使用 Azure VM 作为生成代理](/azure/jenkins/jenkins-azure-vm-agents)
- [使用 Azure CLI 管理作业和管道中的资源](/azure/jenkins/execute-cli-jenkins-pipeline)
 
