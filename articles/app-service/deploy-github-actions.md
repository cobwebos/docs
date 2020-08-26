---
title: 使用 GitHub Actions 配置 CI/CD
description: 了解如何使用 GitHub Actions 从 CI/CD 管道将代码部署到 Azure 应用服务。 自定义生成任务并执行复杂的部署。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 713f4228bc2ba968fc96668d4d5c568f33b7e786
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080277"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub Actions 部署到应用服务

使用 [GitHub Actions](https://help.github.com/en/articles/about-github-actions)，你可以灵活地生成自动化软件开发生命周期工作流。 使用适用于 GitHub 的 Azure 应用服务操作时，可以使用 GitHub Actions 自动执行工作流以部署到 [Azure 应用服务](overview.md)。

> [!IMPORTANT]
> GitHub Actions 当前为 beta 版本。 必须首先使用 GitHub 帐户[注册才能加入预览](https://github.com/features/actions)。
> 

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于 Azure 应用服务工作流，文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体。 <br /> 2. 创建 GitHub 机密。 |
|**生成** | 1. 设置环境。 <br /> 2. 生成 web 应用。 |
|**部署** | 1. 部署 web 应用。 |

## <a name="generate-deployment-credentials"></a>生成部署凭据

# <a name="user-level-credentials"></a>[用户级凭据](#tab/userlevel)

可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/) 中的 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在上面的示例中，将占位符替换为你的订阅 ID、资源组名称和应用名称。 输出是一个具有角色分配凭据的 JSON 对象，该对象提供对应用服务应用的访问权限，如下所示。 稍后复制此 JSON 对象。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 始终应授予最小访问权限。 上一示例中的范围限制为特定的应用服务应用，而不是整个资源组。

# <a name="app-level-credentials"></a>[应用级凭据](#tab/applevel)

你可以使用应用的发布配置文件来使用应用级凭据。 在门户中，切换到应用的 "管理" 页。 在 "**概述**" 页中，单击 "**获取发布配置文件**" 选项。

稍后你将需要该文件的内容。

---

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

# <a name="user-level-credentials"></a>[用户级凭据](#tab/userlevel)

在[GitHub](https://github.com/)中，浏览存储库，选择 "**设置" > 机密 > 添加新机密**。

要使用[用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[应用级凭据](#tab/applevel)

在[GitHub](https://github.com/)中，浏览存储库，选择 "**设置" > 机密 > 添加新机密**。

若要使用[应用级凭据](#generate-deployment-credentials)，请将下载的发布配置文件的内容粘贴到机密的值字段中。 为机密指定名称，如 `azureWebAppPublishProfile` 。

稍后在配置工作流文件时，请使用 " `publish-profile` 部署 Azure Web 应用" 操作的输入的机密。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>设置环境

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

这取决于语言，对于 Azure 应用服务支持的语言，此部分应是每种语言的标准生成步骤。

以下示例显示了以各种受支持的语言生成 Web 应用的工作流部分。

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
## <a name="deploy-to-app-service"></a>部署到应用服务

若要将代码部署到应用服务应用，请使用 `azure/webapps-deploy@v2` 操作。 此操作具有四个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **app-name** | （必需）应用服务应用的名称 | 
| **publish-profile** | （可选）具有 Web 部署机密的发布配置文件内容 |
| **package** | （可选）包或文件夹的路径。 *.zip、*.war、*.jar 或要部署的文件夹 |
| **slot-name** | （可选）输入生产槽以外的现有槽 |

# <a name="user-level-credentials"></a>[用户级凭据](#tab/userlevel)

下面是使用 Azure 服务主体生成 Node.js 应用并将其部署到 Azure 的示例工作流。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[应用级凭据](#tab/applevel)

下面是使用应用的发布配置文件构建 Node.js 应用并将其部署到 Azure 的示例工作流。 请注意 `publish-profile` 输入如何引用 `azureWebAppPublishProfile` 前面创建的机密。

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="next-steps"></a>后续步骤

可在 GitHub 上找到分组到不同存储库中的操作集，其中每一个都包含文档和示例，帮助你将 GitHub 用于 CI/CD 并将应用部署到 Azure。

- [要部署到 Azure 的操作工作流](https://github.com/Azure/actions-workflow-samples)

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [用于容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初学者工作流](https://github.com/actions/starter-workflows)
