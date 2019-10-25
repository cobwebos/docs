---
title: 使用 GitHub 操作从 CI/CD 管道部署代码-Azure App Service |Microsoft Docs
description: 了解如何使用 GitHub 操作将代码部署到应用服务
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: jafreebe
ms.openlocfilehash: b7ec1ae1d04fb1dbe16fd9f4a2640b2b3d9584c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809768"
---
# <a name="github-actions-for-deploying-to-app-service"></a>用于部署到应用服务的 GitHub 操作

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您可以灵活地生成自动软件开发生命周期工作流。 使用 GitHub Azure App Service 操作时，可以使用 GitHub 操作自动化工作流，以便部署[Azure Web 应用](https://azure.microsoft.com/services/app-service/web/)。

> [!IMPORTANT]
> GitHub 操作当前为 beta 版本。 必须首先[注册，才能](https://github.com/features/actions)使用 GitHub 帐户加入预览版。
> 

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于 Azure Web 应用工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体 <br /> 2. 创建 GitHub 机密 |
|**生成** | 1. 设置环境 <br /> 2. 生成 web 应用 |
|**部署** | 1. 部署 web 应用 |

## <a name="create-a-service-principal"></a>创建服务主体

你可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 创建-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令来创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com/)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此示例中，请将资源中的占位符替换为你的订阅 ID、资源组和 web 应用名称。 输出是提供对 Web 应用的访问权限的角色分配凭据。 复制此 JSON 对象，该对象可用于从 GitHub 进行身份验证。

> [!NOTE]
> 如果决定使用发布配置文件进行身份验证，则不需要创建服务主体。

> [!IMPORTANT]
> 授予最小访问权限始终是一种很好的做法。 这就是上一个示例中的作用域仅限于特定的 web 应用，而不是整个资源组。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

你还可以使用应用级凭据，即发布配置文件。 按照以下步骤配置机密：

1. 使用 "**获取发布配置文件**" 选项从门户下载 Web 应用的发布配置文件。

2. 在[GitHub](https://github.com/)中，浏览存储库，选择 "**设置" > 机密 "> 添加新密钥**

    ![机密](media/app-service-github-actions/secrets.png)

3. 将下载的发布配置文件的内容粘贴到机密的值字段中。

4. 现在，在分支中的工作流文件中： `.github/workflows/workflow.yml` 替换 "部署 Azure Web 应用" 操作的输入 `publish-profile` 的密码。
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 在定义后，你将看到如下所示的机密。

    ![机密](media/app-service-github-actions/app-service-secrets.png)

## <a name="setup-the-environment"></a>设置环境

可以使用其中一个安装操作来设置环境。

|**语言**  |**设置操作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

以下示例显示了为各种受支持的语言设置环境的工作流部分：

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>构建 Web 应用

这取决于语言和 Azure Web 应用支持的语言，本节应为每种语言的标准生成步骤。

下面的示例以各种支持的语言显示生成 web 应用的工作流部分。

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-the-web-app"></a>部署 Web 应用

若要将代码部署到 web 应用，需要使用 `Azure/appservice-actions/webapp@master` 操作。 此操作有4个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **应用名称** | 请求Azure Web 应用的名称 | 
| **发布-配置文件** | 可有可无用 Web 部署的机密发布配置文件内容 |
| **package** | 可有可无包或文件夹的路径。 \* .zip、* war、* .jar 或要部署的文件夹 |
| **槽名称** | 可有可无输入生产槽以外的现有槽 |

### <a name="deploy-using-publish-profile"></a>使用发布配置文件进行部署

下面是使用发布配置文件构建 node.js Web 应用并将其部署到 Azure 的示例工作流。

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>使用 Azure 服务主体进行部署

下面是使用 Azure 服务主体构建 node.js Web 应用并将其部署到 Azure 的示例工作流。

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>后续步骤

可在 GitHub 上找到一组分组到不同存储库中的操作，其中每个操作都包含文档和示例，帮助你将 GitHub 用于 CI/CD 并将你的应用部署到 Azure。

- [Azure 登录](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp for 容器](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [Starter 工作流](https://github.com/actions/starter-workflows)
