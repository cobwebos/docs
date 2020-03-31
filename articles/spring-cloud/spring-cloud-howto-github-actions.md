---
title: Azure 春云 CI/CD 与 GitHub 操作
description: 如何通过 GitHub 操作为 Azure 春云构建 CI/CD 工作流
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538458"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure 春云 CI/CD 与 GitHub 操作

GitHub 操作支持自动化软件开发生命周期工作流。 借助 Azure 春云的 GitHub 操作，您可以在存储库中创建工作流，以生成、测试、打包、发布和部署到 Azure。 

## <a name="prerequisites"></a>先决条件
此示例需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="set-up-github-repository-and-authenticate"></a>设置 GitHub 存储库并进行身份验证
您需要 Azure 服务原则凭据来授权 Azure 登录操作。 要获取 Azure 凭据，请在本地计算机上执行以下命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
要访问特定资源组，可以缩小范围：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
该命令应输出 JSON 对象：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例在 GitHub 上使用[Piggy 指标](https://github.com/Azure-Samples/piggymetrics)示例。  分叉示例，打开 GitHub 存储库页面，然后单击 **"设置"** 选项卡。打开 **"打开机密"** 菜单，然后单击"**添加新机密**：

 ![添加新机密](./media/github-actions/actions1.png)

将机密名称`AZURE_CREDENTIALS`及其值设置为您在标题"*设置 GitHub 存储库并进行身份验证*"标题下找到的 JSON 字符串。

 ![设置机密数据](./media/github-actions/actions2.png)

您还可以在 GitHub 操作中从密钥保管库获取 Azure 登录凭据，如[GitHub 操作中使用密钥保管库进行验证 Azure 弹簧中](./spring-cloud-github-actions-key-vault.md)所述。

## <a name="provision-service-instance"></a>预配服务实例
要预配 Azure Spring 云服务实例，请使用 Azure CLI 运行以下命令。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>构建工作流
工作流使用以下选项定义。

### <a name="prepare-for-deployment-with-azure-cli"></a>准备使用 Azure CLI 进行部署
该命令`az spring-cloud app create`当前不是幂等的。  我们建议在现有 Azure 春云应用和实例上使用此工作流。

使用以下 Azure CLI 命令进行准备：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接使用 Azure CLI 部署
在`.github/workflow/main.yml`存储库中创建文件：

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>使用 Azure CLI 操作进行部署
az`run`命令将使用最新版本的 Azure CLI。 如果存在重大更改，也可以将 Azure CLI 的特定版本与 Azure/CLI`action`一起使用。 

> [!Note] 
> 此命令将在新容器中运行，因此`env`无法工作，并且跨操作文件访问可能具有额外的限制。

在存储库中创建 .github/工作流/main.yml 文件：
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>使用 Maven 插件进行部署
另一个选项是使用[Maven 插件](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven)部署 Jar 并更新应用设置。 该命令`mvn azure-spring-cloud:deploy`是幂等的，如果需要，将自动创建应用。 您无需提前创建相应的应用。

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>运行工作流
推送`.github/workflow/main.yml`到 GitHub 后，应自动启用 GitHub**操作**。 当您推送新提交时，将触发该操作。 如果在浏览器中创建此文件，则操作应该已运行。

要验证操作是否已启用，请单击 GitHub 存储库页上的 **"操作"** 选项卡：

 ![验证已启用的操作](./media/github-actions/actions3.png)

如果操作运行错误（例如，如果尚未设置 Azure 凭据），则可以在修复错误后重新运行检查。 在 GitHub 存储库页上，单击 **"操作**"，选择特定的工作流任务，然后单击 **"重新运行检查"** 按钮以重新运行检查：

 ![重新运行检查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>后续步骤
* [春云 GitHub 操作的关键保管库](./spring-cloud-github-actions-key-vault.md)
* [Azure 活动目录服务主体](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Azure 的 GitHub 操作](https://github.com/Azure/actions/)
