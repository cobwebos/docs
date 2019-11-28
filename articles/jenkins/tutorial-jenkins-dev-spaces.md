---
title: 通过 Azure Kubernetes 服务使用适用于 Jenkins 的 Azure Dev Spaces 插件
description: 了解如何在持续集成管道中使用 Azure Dev Spaces 插件。
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 9dba0307db8ebbf07422fd770ea336b2abc031bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209673"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>教程：通过 Azure Kubernetes 服务使用适用于 Jenkins 的 Azure Dev Spaces 插件 

使用 Azure Dev Spaces，可以测试并以迭代方式开发在 Azure Kubernetes 服务 (AKS) 中运行的微服务应用程序，而无需复制或模拟依赖项。 适用于 Jenkins 的 Azure Dev Spaces 插件有助于在持续集成和交付 (CI/CD) 管道中使用 Dev Spaces。

本教程还会使用 Azure 容器注册表 (ACR)。 ACR 存储映像，ACR 任务生成 Docker 和 Helm 项目。 使用 ACR 和 ACR 任务进行项目生成使你无需在 Jenkins 服务器上安装其他软件，例如 Docker。 

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> * 创建支持 Azure Dev Spaces 的 AKS 群集
> * 将多服务应用程序部署到 AKS
> * 准备 Jenkins 服务器
> * 在 Jenkins 管道中使用 Azure Dev Spaces 插件预览代码更改后，再将它们合并到项目中

本教程假设你具备核心 Azure 服务、AKS、ACR、Azure Dev Spaces、Jenkins [管道](https://jenkins.io/doc/book/pipeline/)和插件以及 GitHub 的中级知识。 基本熟悉支持工具（如 kubectl 和 Helm）是有帮助的。

## <a name="prerequisites"></a>先决条件

* 一个 Azure 帐户。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个 GitHub 帐户。 如果没有 GitHub 帐户，请在开始前创建一个[免费帐户](https://github.com/)。

* 已安装 [Visual Studio Code](https://code.visualstudio.com/download) 和 [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 扩展。

* [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)（版本 2.0.43 或更高版本）。

* Jenkins 主服务器。 如果还没有 Jenkins master，在 Azure 上通过执行此 [快速入门](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)中的步骤部署 [Jenkins](https://aka.ms/jenkins-on-azure) 。 

* Helm 和 kubectl 必须已安装在 Jenkins 服务器上并可供 Jenkins 帐户使用，正如本教程后面部分所述。

* VS Code、VS Code 终端或 WSL 和 Bash。 


## <a name="create-azure-resources"></a>创建 Azure 资源

在此部分，需要创建 Azure 资源：

* 包含本教程的所有 Azure 资源的资源组。
* [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/) (AKS) 群集。
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/) (ACR)，用于生成（使用 ACR 任务）和存储 Docker 映像。

1. 创建资源组。

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. 创建 AKS 群集。 在[支持 Dev Spaces 的区域](../dev-spaces/about.md#supported-regions-and-configurations)创建 AKS 群集。

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. 配置 AKS 以使用 Dev Spaces。

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    此步骤安装 `azds` CLI 扩展。

4. 创建容器注册表。

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>将示例应用部署到 AKS 群集

在本部分中，你将设置一个开发空间，将示例应用程序部署到在上一部分中创建的 AKS 群集中。 应用程序由两部分组成：webfrontend 和 mywebapi   。 这两个组件均部署在开发空间中。 稍后在本教程中，你将提交一个针对 mywebapi 的拉取请求，以触发 Jenkins 中的 CI 管道。

要获取有关 Azure Dev Spaces 的使用和使用 Azure Dev Spaces 的多服务开发的详细信息，请参阅[通过 Java 开始使用 Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/get-started-java) 和[使用 Azure Dev Spaces 的多服务开发](https://docs.microsoft.com/azure/dev-spaces/multi-service-java)。 这些教程提供了此处没有的其他背景信息。

1. 从 GitHub 下载 https://github.com/Azure/dev-spaces 存储库。

2. 在 VS Code 中打开 `samples/java/getting-started/webfrontend` 文件夹。 （可以忽略任何默认提示以添加调试资产或还原项目。）

3. 将 `/src/main/java/com/ms/sample/webfrontend/Application.java` 更新为如下所示：

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. 依次单击“查看”、“终端”以在 VS Code 中打开集成终端。  

5. 运行 `azds prep` 命令以准备应用程序在开发空间中运行。 此命令必须从 `dev-spaces/samples/java/getting-started/webfrontend` 运行以正确准备你的应用程序：

    ```bash
    azds prep --public
    ```

    Dev Spaces CLI 的 `azds prep` 命令使用默认设置生成 Docker 和 Kubernetes 资产。 这些文件在项目的生命周期内持续保留，并且可自定义为：

    * `./Dockerfile` 和 `./Dockerfile.develop` 描述应用的容器映像，以及如何在容器内生成和运行源代码。
    * `./charts/webfrontend` 下面的 [Helm 图表](https://helm.sh/docs/topics/charts/)描述如何将容器部署到 Kubernetes。
    * `./azds.yaml` 是 Azure Dev Spaces 的配置文件。

    若要了解详情，请参阅 [Azure Dev Spaces 的工作原理及其配置方式](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)。

6. 使用 `azds up` 命令在 AKS 中生成并运行应用程序：

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>扫描控制台输出以获取有关由 `up` 命令创建的 URL 的信息。 它的形式如下：

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    在浏览器窗口中打开此 URL，你应看到 Web 应用。 在容器执行时，`stdout` 和 `stderr` 输出将流式传输到终端窗口。

8. 接下来，设置并部署 mywebapi  ：

    1. 将目录更改为 `dev-spaces/samples/java/getting-started/mywebapi`

    2. 运行

        ```bash
        azds prep
        ```

    3. 运行

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>准备 Jenkins 服务器

在本部分中，需要准备要运行示例 CI 管道的 Jenkins 服务器。

* 安装插件
* 安装 Helm 和 Kubernetes CLI
* 添加凭据

### <a name="install-plug-ins"></a>安装插件

1. 登录 Jenkins 服务器。 选择“管理 Jenkins”>“管理插件”。 
2. 在“可用”选项卡上选择以下插件： 
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure 容器注册表任务](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [环境注入程序](https://plugins.jenkins.io/envinject)
    * [GitHub 集成](https://plugins.jenkins.io/github-pullrequest)

    如果这些插件没有显示在列表中，请检查“已安装”  选项卡以查看它们是否已安装。

3. 若要安装插件，请选择“立即下载并在重启后安装”。 

4. 重启 Jenkins 服务器以完成安装。

### <a name="install-helm-and-kubectl"></a>安装 Helm 和 kubectl

示例管道使用 Helm 和 kubectl 部署到开发空间。 Jenkins 安装完成时，它将创建名为 jenkins 的管理员帐户  。 Helm 和 kubectl 需要可供 jenkins 用户访问。

1. 与 Jenkins master 建立 SSH 连接。 

2. 切换到 `jenkins` 用户：
    ```bash
    sudo su jenkins
    ```

3. 安装 Helm CLI。 有关详细信息，请参阅[安装 Helm](https://helm.sh/docs/using_helm/#installing-helm)。

4. 安装 kubectl。 有关详细信息，请参阅 [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm)。

### <a name="add-credentials-to-jenkins"></a>将凭据添加到 Jenkins

1. Jenkins 需要 Azure 服务主体用于身份验证和访问 Azure 资源。 若要创建服务主体，请参阅“部署到 Azure 应用服务”教程中的 [创建服务主体](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) 部分。 请务必保存来自 `create-for-rbac` 的输出的副本，因为你需要该信息来完成下一步。 输出将如下所示：

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. 使用先前步骤中的服务主体信息，在 Jenkins 中添加“Microsoft Azure 服务主体”凭据类型  。 下面的屏幕截图中的名称对应来自 `create-for-rbac` 的输出。

    ID 字段是服务主体的 Jenkins 凭据名称  。 该示例使用 `displayName` 的值（在本例中为 `xxxxxxxjenkinssp`），但你可以使用你想使用的任何文本。 此凭据名称是下一部分中的 AZURE_CRED_ID 环境变量的值。

    ![将服务主体凭据添加到 Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    “说明”是可选的  。 有关详细说明，请参阅“部署到 Azure 应用服务”教程中的[将服务主体添加到 Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins)  部分。 



3. 若要显示 ACR 凭据，请运行以下命令：

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    复制 JSON 输出，应如下所示：

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. 在 Jenkins 中添加“带密码的用户名”凭据类型  。 `acr01` 示例中，用户名是上一步中的用户名  。 在示例 `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz` 中，密码  是第一个密码的值。 此凭据的 ID 是 ACR_CRED_ID 的值  。

5. 设置一个 AKS 凭据。 在 Jenkins 中添加 Kubernetes 配置 (kubeconfig) 凭据类型（使用“直接输入”选项）  。 若要获取 AKS 群集的访问凭据，请运行以下命令：

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   此凭据的 ID 是下一部分中 KUBE_CONFIG_ID 的值  。

## <a name="create-a-pipeline"></a>创建管道

为示例管道选择的方案基于实际模式：拉取请求触发 CI 管道，该管道生成建议的更改并将其部署到 Azure 开发空间进行测试和评审。 这些更改要么合并并部署到 AKS 要么被丢弃，具体取决于评审的结果。 最后，该开发空间被删除。

Jenkins 管道配置和 Jenkinsfile 定义 CI 管道中的阶段。 此流程图显示了管道阶段和由 Jenkinsfile 定义的决策点：

![Jenkins 管道流](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. 从 https://github.com/azure-devops/mywebapi 下载  mywebapi 项目的已修改版本。 此项目包含创建管道所需的一系列文件，包括 Jenkinsfile，Dockerfile 和 Helm 图表   。

2. 登录到 Jenkins。 从左侧菜单中选择“添加项”  。

3. 选择“管道”然后在“输入项名称”框中输入名称   。 选择“确定”  ，然后管道配置屏幕将自动打开。

4. 在“常规”选项卡上，勾选“准备运行的环境”。   

5. 勾选“保留 Jenkins 环境变量”和“保留 Jenkins 生成变量”   。

6. 在“属性内容”框中，输入以下环境变量： 

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    使用前面部分中提供的示例值，环境变量的列表应如下所示：

    ![Jenkins 管道环境变量](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. 选择“管道”>“定义”中的“来自 SCM 的管道脚本”。  
8. 在“SCM”中，选择“Git”然后输入存储库 URL。  
9. 在“分支说明符”中，  输入 `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`。
10. 填写 SCM 存储库 URL 和脚本路径“Jenkinsfile”。
11. 应勾选“轻量签出”  。

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>创建拉取请求来触发管道

要完成本部分中的步骤 3，需要注释 Jenkinsfile 的一部分，否则在尝试同时查看新版本和旧版本时，将出现 404 错误。 默认情况下，当选择合并拉取请求时，先前的 mywebapi 共享版本将被删除并替代为新版本。 在完成步骤 1 之前，请对 Jenkinsfile 进行以下更改：

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. 更改 `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`，然后创建拉取请求。 例如：

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. 登录到 Jenkins 并选择管道名称，然后选择“立即生成”  。 

    此外还可以设置 webhook 自动触发 Jenkins 管道  。 输入拉取请求后，GitHub 会向 Jenkins 发出一个 POST，触发管道。 有关设置 webhook 的详细信息，请参阅[将 Jenkins 连接到 GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github)。

3. 比较对当前共享版本的更改：

    1. 打开浏览器并导航到共享版本 `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`。 TEST_ENDPOINT 包含 URL。

    2. 打开另一个选项卡，然后输入拉取请求的开发空间 URL。 该属性类似于 `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`。 你会在“生成历史记录”>“<build#>”>“控制台输出”中找到 Jenkins 作业的链接。  在页面上搜索 `aksapp`，或搜索 `azdsprefix` 只查看前缀。

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>构造子开发空间的 URL

当你提出拉取请求时，Jenkins 会在团队的共享开发空间上创建一个子开发空间，并从该子开发空间中的拉取请求运行代码。 子开发空间的 URL 采用 `http://$env.azdsprefix.<test_endpoint>` 形式。 

$env.azdsprefix 在管道执行期间由 Azure Dev Spaces 插件通过 devSpacesCreate 设置   ：

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` 是你之前在[将示例应用部署到 AKS 集群，步骤 7](#test_endpoint)中使用 `azds up` 部署的 webfrontend 应用的 URL。 `$env.TEST_ENDPOINT` 的值在管道配置中设置。 

以下代码片段演示子开发空间 URL 在 `smoketest` 阶段是如何使用的。 代码检查子开发空间 TEST_ENDPOINT 是否可用，如果可用，会将问候语文本下载到 stdout：

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>清理资源

示例应用程序使用完成后，通过删除资源组来清理 Azure 资源：

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用适用于 Jenkins 的 Azure Dev Spaces 插件和 Azure 容器注册表插件来生成代码并部署到开发空间。

下面的资源列表提供了关于 Azure Dev Spaces、ACR 任务以及 CI/CD 与 Jenkins 配合使用的更多信息。

Azure Dev Spaces：
* [Azure Dev Spaces如何工作和配置](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR 任务：
* [使用 ACR 任务自动执行 OS 和框架修补](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [提交源代码时自动生成](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

在 Azure 上将 CI/CD 与 Jenkins 配合使用：
* [Jenkins 持续部署](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
