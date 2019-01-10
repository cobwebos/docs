---
title: 教程 - 使用 Jenkins 从 GitHub 部署到 Azure 应用服务
description: 设置 Jenkins 以便从 GitHub 持续集成 (CI) 和持续部署 (CD) 到适用于 Java Web 应用的 Azure 应用服务
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: 786fc427e4cb631c4aedd0f795daebebd9fb15bc
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077430"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>教程：使用 Jenkins 持续集成和部署从 GitHub 部署到 Azure 应用服务

本教程通过设置 Jenkins 中的持续集成 (CI) 和持续部署 (CD)，将 GitHub 中的一个示例 Java Web 应用部署到 [Linux 上的 Azure 应用服务](/azure/app-service/containers/app-service-linux-intro)。 当你通过将提交内容推送到 GitHub 来更新应用时，Jenkins 会自动生成该应用并将其重新发布到 Azure 应用服务。 本教程中的示例应用是使用 [Spring Boot](http://projects.spring.io/spring-boot/) 框架开发的。 

![概述](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> * 安装 Jenkins 插件，以便可以从 GitHub 生成应用、将应用部署到 Azure 应用服务，以及执行其他相关任务。
> * 分叉示例 GitHub 存储库，以获得一个工作副本。
> * 将 Jenkins 连接到 GitHub。
> * 创建 Azure 服务主体，使 Jenkins 能够访问 Azure，且无需使用你的凭据。
> * 将服务主体添加到 Jenkins。
> * 创建 Jenkins 管道，以便每次更新 GitHub 中的应用时，都会生成并部署示例应用。
> * 创建 Jenkins 管道的生成和部署文件。
> * 将 Jenkins 管道指向生成和部署脚本。
> * 运行手动生成，将示例应用部署到 Azure。
> * 在 GitHub 中推送应用更新，触发 Jenkins 以生成应用并将其重新部署到 Azure。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要准备好以下各项：

* 在 Azure Linux VM 上安装一个包含 Java 开发工具包 (JDK) 和 Maven 工具的 [Jenkins](https://jenkins.io/) 服务器

  如果没有 Jenkins 服务器，请立即在 Azure 门户中完成以下步骤：[在 Azure Linux VM 上创建 Jenkins 服务器](/azure/jenkins/install-jenkins-solution-template)

* 创建一个 [GitHub](https://github.com) 帐户，以便可以获取示例 Java Web 应用的工作副本（[分叉](#fork)）。 

* 可以从本地命令行或 [Azure Cloud Shell](/azure/cloud-shell/overview) 运行的 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="install-jenkins-plug-ins"></a>安装 Jenkins 插件

1. 登录到 Jenkins Web 控制台，位置如下：

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. 在 Jenkins 主页上，选择“管理 Jenkins” > “管理插件”。

   ![管理 Jenkins 插件](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. 在“可用”选项卡上选择以下插件：

   - [Azure 应用服务](https://plugins.jenkins.io/azure-app-service)
   - [GitHub 分支源](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [环境注入器插件](https://plugins.jenkins.io/envinject)
   - [Azure 凭据](https://plugins.jenkins.io/azure-credentials)

   如果未显示这些插件，请在“已安装”选项卡中确认它们是否尚未安装。

1. 若要安装所选的插件，请选择“立即下载并在重启后安装”。

1. 完成后，在 Jenkins 菜单中选择“管理 Jenkins”，以返回到 Jenkins 管理页来完成后续步骤。

## <a name="fork-sample-github-repo"></a>分叉示例 GitHub 存储库

1. [登录到 Spring Boot 示例应用的 GitHub 存储库](https://github.com/spring-guides/gs-spring-boot)。 

1. 在 GitHub 的右上角，选择“分叉”。

   ![从 GitHub 分叉示例存储库](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. 遵照提示选择 GitHub 帐户并完成分叉。

接下来，使用 GitHub 凭据设置 Jenkins。

## <a name="connect-jenkins-to-github"></a>将 Jenkins 连接到 GitHub

若要让 Jenkins 监视 GitHub 并在将新提交内容推送到 GitHub 分叉中的 Web 应用时做出响应，请在 Jenkins 中启用 [GitHub Webhook](https://developer.github.com/webhooks/)。

> [!NOTE]
> 
> 这些步骤将创建个人访问令牌凭据，使 Jenkins 能够使用你的 GitHub 用户名和密码来利用 GitHub。 
> 但是，如果你的 GitHub 帐户使用双重身份验证，请在 GitHub 中创建令牌，并将 Jenkins 设置为改用该令牌。 
> 有关详细信息，请参阅 [Jenkins GitHub 插件](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)文档。

1. 在“管理 Jenkins”页中，选择“配置系统”。 

   ![配置系统](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. 在“GitHub”部分，提供 GitHub 服务器的详细信息。 在“添加 GitHub 服务器”列表中，选择“GitHub 服务器”。 

   ![添加 GitHub 服务器](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. 如果未选择“管理挂钩”属性，请选择此属性。 选择“高级”，以便可以指定其他设置。 

   ![选择“高级”以指定其他设置](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. 在“管理其他 GitHub 操作”列表中，选择“将登录名和密码转换为令牌”。

   ![选择“管理其他 GitHub 操作”](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. 选择“使用登录名和密码”，以便可以输入 GitHub 用户名和密码。 完成后，选择“创建令牌凭据”，以创建 [GitHub 个人访问令牌 (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。   

   ![使用登录名和密码创建 GitHub PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. 在“GitHub 服务器”部分的“凭据”列表中，选择新令牌。 选择“测试连接”，检查是否可正常执行身份验证。

   ![使用新 PAT 检查 GitHub 服务器的连接](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

接下来，创建由 Jenkins 用来进行身份验证和访问 Azure 资源的 Azure 服务主体。

## <a name="create-service-principal"></a>创建服务主体

在后一部分，你将创建一个 Jenkins 管道作业，用于从 GitHub 生成应用并将其部署到 Azure 应用服务。 若要让 Jenkins 访问 Azure 且无需输入凭据，请在 Azure Active Directory 中为 Jenkins 创建一个[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。 服务主体是一个独立的标识，Jenkins 可以使用它来对 Azure 资源访问进行身份验证。 若要创建此服务主体，请从本地命令行或 Azure Cloud Shell 运行 Azure CLI 命令 [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)，例如： 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

请务必将服务主体名称括在引号中。 另外，请根据 [Azure Active Directory 密码规则和限制](/azure/active-directory/active-directory-passwords-policy)创建强密码。 如果未提供密码，Azure CLI 会自动创建密码。 

下面是 **`create-for-rbac`** 命令生成的输出： 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> 如果已有一个服务主体，则可以重用该标识。
> 提供用于身份验证的服务主体值时，请使用 `appId`、`password` 和 `tenant` 属性值。 
> 搜索现有服务主体时，请使用 `displayName` 属性值。

## <a name="add-service-principal-to-jenkins"></a>将服务主体添加到 Jenkins

1. 在 Jenkins 主页上，选择“凭据” > “系统”。 

1. 在“系统”页上的“域”下，选择“全局凭据(无限制)”。

1. 在左侧菜单中，选择“添加凭据”。

1. 在“类型”列表中，选择“Azure 服务主体”。

1. 在此步骤的表格所述的属性中提供服务主体和 Azure 订阅的信息：

   ![添加 Azure 服务主体凭据](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | 属性 | 值 | Description | 
   |----------|-------|-------------| 
   | **订阅 ID** | <*yourAzureSubscription-ID*> | 你的 Azure 订阅的 GUID 值 <p>**提示**：如果你不知道自己的 Azure 订阅 ID，请从命令行或 Cloud Shell 运行以下 Azure CLI 命令，然后使用 `id` GUID 值： <p>`az account list` | 
   | **客户端 ID** | <*yourAzureServicePrincipal-ID*> | 之前为 Azure 服务主体生成的 `appId` GUID 值 | 
   | **客户端机密** | <*yourSecurePassword*> | 为 Azure 服务主体提供的 `password` 值或“机密” | 
   | **租户 ID** | <*yourAzureActiveDirectoryTenant-ID*> | Azure Active Directory 租户的 `tenant` GUID 值 | 
   | **ID** | <*yourAzureServicePrincipalName*> | Azure 服务主体的 `displayName` 值 | 
   |||| 

1. 若要确认服务主体是否正常工作，请选择“验证服务主体”。 完成后，选择“确定”。

接下来，创建用于生成和部署应用的 Jenkins 管道。

## <a name="create-jenkins-pipeline"></a>创建 Jenkins 管道

在 Jenkins 中，创建用于生成和部署应用的管道作业。

1. 返回到 Jenkins 主页，选择“新建项”。 

   ![选择“新建项”](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. 为管道作业提供一个名称（例如“My-Java-Web-App”），然后选择“管道”。 在底部选择“确定”。  

   ![选择“管道”](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. 使用服务主体设置 Jenkins，使 Jenkins 能够部署到 Azure，且无需使用你自己的凭据。

   1. 在“常规”选项卡上，选择“准备运行的环境”。 

   1. 在显示的“属性内容”框中，添加以下环境变量及其值。 

      ```text
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![选择“准备运行的环境”并设置环境变量](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. 完成后，选择“保存”。

接下来，为 Jenkins 创建生成和部署脚本。

## <a name="create-build-and-deployment-files"></a>创建生成和部署文件

现在，创建可由 Jenkins 用来生成和部署应用的文件。

1. 在 GitHub 分叉的 `src/main/resources/` 文件夹中，创建名为 `web.config` 的应用配置文件，其中包含以下 XML（但要将 `$(JAR_FILE_NAME)` 替换为 `gs-spring-boot-0.1.0.jar`）：

   ```xml
   <?xml version="1.0" encoding="UTF-8">
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. 在 GitHub 分支的根文件夹中，创建名为 `Jenkinsfile` 的生成和部署脚本，其中包含以下文本（[GitHub 中的源代码位于此处](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)）：

   ```text  
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. 将 `web.config` 和 `Jenkinsfile` 文件提交到 GitHub 分支，并推送更改。

## <a name="point-pipeline-at-script"></a>将管道指向脚本

现在，指定要让 Jenkins 使用的生成和部署脚本。

1. 在 Jenkins 中，选择前面创建的管道作业。 

   ![选择 Web 应用的管道作业](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. 在左侧菜单中，选择“配置”。

1. 在“管道”选项卡上的“定义”列表中，选择“来自 SCM 的管道脚本”。

   1. 在显示的“SCM”框中，选择“Git”作为源代码管理。 

   1. 在“存储库”部分，对于“存储库 URL”，请输入 GitHub 分叉的 URL，例如： 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. 对于“凭据”，请选择前面创建的 GitHub 个人访问令牌。

   1. 在“脚本路径”框中，添加“Jenkinsfile”脚本的路径。

   完成后，管道定义如以下示例所示： 

   ![将管道指向脚本](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. 完成后，选择“保存”。

接下来，生成应用并将其部署到 Azure 应用服务。 

## <a name="build-and-deploy-to-azure"></a>生成并部署到 Azure

1. 从命令行或 Azure Cloud Shell 使用 Azure CLI，[在 Linux 上创建一个 Azure 应用服务 Web 应用](/azure/app-service/containers/app-service-linux-intro)。生成后，Jenkins 将部署该 Web 应用。 请确保 Web 应用的名称唯一。

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   有关这些 Azure CLI 命令的详细信息，请参阅以下页面：

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. 在 Jenkins 中选择你的管道作业，然后选择“立即生成”。

   生成完成后，Jenkins 将部署该应用。该应用随即会使用发布 URL 在 Azure 中上线，例如： 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![查看 Azure 上已部署的应用](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>推送更改并重新部署

1. 在 Web 浏览器中，转到 Web 应用 GitHub 分叉中的以下位置：

   `complete/src/main/java/Hello/Application.java`
   
1. 在 GitHub 的右上角，选择“编辑此文件”。

1. 对 `commandLineRunner()` 方法进行以下更改，然后将更改提交到存储库的 `master` 分支。 提交到 `master` 分支会在 Jenkins 中启动生成。 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. 完成生成并且在 Jenkins 将应用重新部署到 Azure 之后，请刷新应用，它现在会显示更新的内容。

   ![查看 Azure 上已部署的应用](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>排查 Jenkins 插件问题

如果 Jenkins 插件出现任何 bug，请在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 中提出特定组件的问题。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure VM 作为生成代理](/azure/jenkins/jenkins-azure-vm-agents)