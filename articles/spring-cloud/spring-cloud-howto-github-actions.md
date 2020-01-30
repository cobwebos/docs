---
title: 带有 GitHub 操作的 Azure 春季 CI/CD
description: 如何为 Azure 春季 Cloud 构建 CI/CD 工作流，包含 GitHub 操作
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 303f24ef6d934c0382bd8917833e3ec545f2a540
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776477"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>带有 GitHub 操作的 Azure 春季 CI/CD

GitHub 操作支持自动化软件开发生命周期工作流。 借助适用于 Azure 春季云的 GitHub 操作，你可以在存储库中创建工作流，以生成、测试、打包、发布和部署到 Azure。 

## <a name="prerequisites"></a>必备组件
此示例需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="set-up-github-repository-and-authenticate"></a>设置 GitHub 存储库并进行身份验证
需要使用 Azure 服务主体凭据来授权 Azure 登录操作。 若要获取 Azure 凭据，请在本地计算机上执行以下命令：
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
若要访问特定的资源组，可以缩小范围：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
该命令应输出一个 JSON 对象：
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

此示例使用 GitHub 上的[非法携带指标](https://github.com/Azure-Samples/piggymetrics)示例。  分叉示例，打开 "GitHub 存储库" 页，然后单击 "**设置**" 选项卡。打开**机密**菜单，并单击**添加新机密**：

 ![添加新机密](./media/github-actions/actions1.png)

将 "机密名称" 设置为 "`AZURE_CREDENTIALS`"，并将其值设置为在 "*设置 GitHub 存储库" 和 "身份验证*" 标题下找到的 JSON 字符串。

 ![设置机密数据](./media/github-actions/actions2.png)

你还可以从 GitHub 操作 Key Vault 获取 Azure 登录凭据，如在[Github 操作中对 Azure Key Vault 春季进行身份验证](./spring-cloud-github-actions-key-vault.md)中所述。

## <a name="provision-service-instance"></a>设置服务实例
若要预配 Azure 春季云服务实例，请使用 Azure CLI 运行以下命令。
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>生成工作流
使用以下选项定义工作流。

### <a name="prepare-for-deployment-with-azure-cli"></a>准备部署 Azure CLI
命令 `az spring-cloud app create` 当前不是幂等的。  建议将此工作流用于现有的 Azure 春季云应用和实例。

使用以下 Azure CLI 命令进行准备：
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>直接部署 Azure CLI
在存储库中创建 `.github/workflow/main.yml` 文件：

```
name: AzureSpringCloud

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
### <a name="deploy-with-azure-cli-action"></a>部署 Azure CLI 操作
Az `run` 命令将使用最新版本的 Azure CLI。 如果有重大更改，还可以将特定版本的 Azure CLI 与 Azure/CLI `action`配合使用。 

> [!Note] 
> 此命令将在新的容器中运行，因此 `env` 将不起作用，并且交叉操作文件访问可能会产生额外的限制。

在存储库中创建 github/workflow/docker-compose.override.yml 文件：
```
name: AzureSpringCloud

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

## <a name="deploy-with-maven-plugin"></a>部署 with Maven 插件
另一种方法是使用[Maven 插件](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven)部署 Jar 和更新应用设置。 命令 `mvn azure-spring-cloud:deploy` 是幂等的，如果需要，将自动创建应用。 无需提前创建相应应用。

```
name: AzureSpringCloud

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
将 `.github/workflow/main.yml` 推送到 GitHub 后，应自动启用 GitHub**操作**。 推送新提交时，将触发该操作。 如果在浏览器中创建此文件，则操作应已运行。

若要验证是否已启用此操作，请单击 "GitHub 存储库" 页上的 "**操作**" 选项卡：

 ![验证操作是否已启用](./media/github-actions/actions3.png)

如果操作以错误运行，例如，如果尚未设置 Azure 凭据，则可以在修复错误后重新运行检查。 在 "GitHub 存储库" 页上，单击 "**操作**"，选择特定的工作流任务，然后单击 "**重新运行检查**" 按钮以重新运行检查：

 ![重新运行检查](./media/github-actions/actions4.png)

## <a name="next-steps"></a>后续步骤
* [春季云 GitHub 操作的 Key Vault](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory 服务主体](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [适用于 Azure 的 GitHub 操作](https://github.com/Azure/actions/)
