---
title: "使用 Jenkins 执行 Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI 通过 Jenkins 管道将 Java Web 应用部署到 Azure"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: a1f5329e4e33ae20541e2fdaa09f4609296bddd5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>使用 Jenkins 和 Azure CLI 部署到 Azure App Service
若要将 Java Web 应用部署到 Azure，可以通过 [Jenkins 管道](https://jenkins.io/doc/book/pipeline/)使用 Azure CLI。 本教程介绍如何在 Azure VM 上创建 CI/CD 管道，包括如何：

> [!div class="checklist"]
> * 创建 Jenkins VM
> * 配置 Jenkins
> * 在 Azure 中创建 Web 应用
> * 准备 GitHub 存储库
> * 创建 Jenkins 管道
> * 运行管道并验证 Web 应用

本教程需要 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>创建和配置 Jenkins 实例
如果还没有 Jenkins Master，请先使用[解决方案模板](install-jenkins-solution-template.md)，该模板默认包含所需的 [Azure 凭据](https://plugins.jenkins.io/azure-credentials)插件。 

使用 Azure 凭据插件可将 Microsoft Azure 服务主体凭据存储在 Jenkins 中。 1.2 版中已添加相应支持，以便 Jenkins 管道获取 Azure 凭据。 

确保有 1.2 版或更高版本：
* 在 Jenkins 仪表板中，单击“Manage Jenkins”（管理 Jenkins）->“Plugin Manager”（插件管理器），搜索“Azure Credential”（Azure 凭据）。 
* 如果版本低于 1.2，请更新插件。

Jenkins Master 还需要 Java JDK 和 Maven。 若要安装，请使用 SSH 登录到 Jenkins Master，并运行以下命令：
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>将 Azure 服务主体添加到 Jenkins 凭据

需要 Azure 凭据才能执行 Azure CLI。

* 在 Jenkins 仪表板中，单击“Credentials”（凭据）->“System”（系统）。 单击“Global credentials(unrestricted)”（全局凭据(不受限制)）。
* 单击“Add Credentials”（添加凭据），通过填写订阅 ID、客户端 ID、客户端密码和 OAuth 2.0 令牌终结点，添加 [Microsoft Azure 服务主体](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)。 提供一个 ID 供后续步骤使用。

![添加凭据](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>创建 Azure App Service 以部署 Java Web 应用

使用 [az appservice plan create](/cli/azure/appservice/plan#create) CLI 命令通过“免费”定价层创建 Azure 应用服务计划。 appservice 计划定义用于托管应用的物理资源。 分配到 appservice 计划的所有应用程序共享这些资源，因此在托管多个应用时可以节省成本。 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

计划就绪后，Azure CLI 显示与以下示例类似的输出：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>创建 Azure Web 应用

 使用 [az webapp create](/cli/azure/appservice/web#create) CLI 命令，在 `myAppServicePlan` 应用服务计划中创建 Web 应用定义。 Web 应用定义提供了一个用于访问应用程序的 URL，并配置了多个将代码部署到 Azure 的选项。 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

将占位符 `<app_name>` 替换为你自己的唯一应用名称。 该唯一名称是 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 可以先将任何自定义域名条目映射到 Web 应用，然后向用户公开该条目。

在 Web 应用定义就绪后，Azure CLI 将显示类似于以下示例的信息： 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>配置 Java 

使用 [az appservice web config update](/cli/azure/appservice/web/config#update) 命令，设置应用所需的 Java 运行时配置。

以下命令配置的 Web 应用可在最新的 Java 8 JDK 和 [Apache Tomcat](http://tomcat.apache.org/) 8.0 上运行。

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>准备 GitHub 存储库
打开[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)存储库。 要将存储库分叉到自己的 GitHub 帐户，请单击右上角的“分叉”按钮。

* 在 GitHub Web UI 中，打开 **Jenkinsfile** 文件。 单击铅笔图标编辑此文件，分别更新第 20 行和第 21 行上的资源组和 Web 应用名称。

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* 更改第 23 行，以更新 Jenkins 实例中的凭据 ID

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>创建 Jenkins 管道
在 Web 浏览器中打开 Jenkins，单击“New Item”（新建项）。 

* 为该作业提供一个名称并选择“Pipeline”（管道）。 单击 **“确定”**。
* 接下来单击“Pipeline”（管道）选项卡。 
* 对于“Definition”（定义），选择“Pipeline script from SCM”（来自 SCM 的管道脚本）。
* 对于“SCM”，选择“Git”。
* 输入分叉存储库的 GitHub URL：https:\<your forked repo\>.git
* 单击“保存”

## <a name="test-your-pipeline"></a>测试管道
* 转到所创建的管道，单击“Build Now”（立即生成）
* 生成操作应该会在几秒钟内成功完成，可以转到该生成，单击“Console Output”（控制台输出）查看详细信息

## <a name="verify-your-web-app"></a>验证 Web 应用
确保 WAR 文件已成功部署到 Web 应用。 打开 Web 浏览器：

* 转到 http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
可看到：

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* 转到 http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>（将 &lt;x> 和 &lt;y> 替换为任意数字）获取 x 与 y 的和

![计算器：加](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>部署到 Linux 上的 Azure Web 应用
了解如何通过 Jenkins 管道使用 Azure CLI 后，就可以修改脚本，部署到 Linux 上的 Azure Web 应用。

Linux 上的 Web 应用支持用另一种方式执行部署，即，使用 Docker。 若要进行部署，需提供 Dockerfile，以便使用服务运行时将 Web 应用打包到 Docker 映像中。 该插件随后生成映像，将其推送到 Docker 注册表，并将映像部署到 Web 应用。

* 按照[此处](../app-service/containers/quickstart-nodejs.md)的步骤操作，创建在 Linux 上运行的 Azure Web 应用。
* 按照[此文章](https://docs.docker.com/engine/installation/linux/ubuntu/)中的说明在 Jenkins 实例上安装 Docker。
* 通过执行[此处](/azure/container-registry/container-registry-get-started-azure-cli)的步骤，在 Azure 门户中创建容器注册表。
* 在同一个已分叉的[适用于 Azure 的简单 Java Web 应用](https://github.com/azure-devops/javawebappsample)存储库中，编辑 **Jenkinsfile2** 文件：
    * 第 18-21 行，分别更新为资源组、Web 应用和 ACR 的名称。 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * 第 24 行，将 \<azsrvprincipal\> 更新为凭据 ID
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* 与在部署到 Windows 中的 Azure Web 应用时一样，创建新的 Jenkins 管道，只不过这一次改用 **Jenkinsfile2**。
* 运行新作业。
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
在本教程中，配置了一个 Jenkins 管道，用于签出 GitHub 存储库中的源代码。 运行 Maven 以生成 war 文件，然后使用 Azure CLI 部署到 Azure App Service。 你已了解如何：

> [!div class="checklist"]
> * 创建 Jenkins VM
> * 配置 Jenkins
> * 在 Azure 中创建 Web 应用
> * 准备 GitHub 存储库
> * 创建 Jenkins 管道
> * 运行管道并验证 Web 应用

