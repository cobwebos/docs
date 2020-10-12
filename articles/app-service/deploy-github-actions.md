---
title: 使用 GitHub Actions 配置 CI/CD
description: 了解如何使用 GitHub Actions 从 CI/CD 管道将代码部署到 Azure 应用服务。 自定义生成任务并执行复杂的部署。
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure
ms.openlocfilehash: 2d28d8f1f09814822b29e9d45d4e75283c8955cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618737"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub Actions 部署到应用服务

通过 [Github 操作](https://help.github.com/en/articles/about-github-actions) 开始，可自动执行工作流，并从 GitHub 部署到 [Azure App Service](overview.md) 。 

## <a name="prerequisites"></a>必备条件 

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 GitHub 帐户。 如果没有，请 [免费](https://github.com/join)注册。  
- 有效 Azure App Service 应用。 
    - .NET： [在 Azure 中创建 ASP.NET Core web 应用](quickstart-dotnetcore.md)
    - ASP.NET： [在 Azure 中创建 ASP.NET Framework web 应用](quickstart-dotnet-framework.md)
    - JavaScript： [在 Azure App Service 中创建 Node.js web 应用](quickstart-nodejs.md)  
    - Java： [在 Azure App Service 上创建 Java 应用](quickstart-java.md)
    - Python： [在 Azure App Service 中创建 Python 应用](quickstart-python.md)

## <a name="workflow-file-overview"></a>工作流文件概述

Azure App Service 工作流文件包含三个部分：

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体或发布配置文件。 <br /> 2. 创建 GitHub 机密。 |
|**生成** | 1. 设置环境。 <br /> 2. 生成 web 应用。 |
|**部署** | 1. 部署 web 应用。 |

## <a name="use-the-deployment-center"></a>使用部署中心

你可以使用应用服务部署中心快速开始使用 GitHub 操作。 这会根据应用程序堆栈自动生成工作流文件，并将其提交到正确目录中的 GitHub 存储库。

1. 导航到 Azure 门户中的 webapp
1. 在左侧，单击 "**部署中心**"
1. 在 " **连续部署 (CI/CD") **中，选择 **GitHub**
1. 接下来，选择 **GitHub 操作**
1. 使用 dropdown 选择 GitHub 存储库、分支和应用程序堆栈
    - 如果所选分支受到保护，则仍可继续添加工作流文件。 继续操作之前，请务必查看分支保护。
1. 在最后一个屏幕上，可以查看所选内容并预览将提交到存储库的工作流文件。 如果选择正确，请单击 "**完成**"

这会将工作流文件提交到存储库。 用于生成和部署应用的工作流将立即启动。

## <a name="set-up-a-work-manually"></a>手动设置工作

你还可以在不使用部署中心的情况下部署工作流。 为此，需要首先生成部署凭据。 

## <a name="generate-deployment-credentials"></a>生成部署凭据

使用 GitHub 操作 Azure 应用服务进行身份验证的建议方法是使用发布配置文件。 你还可以使用服务主体进行身份验证，但该过程需要更多步骤。 

保存你的发布配置文件凭据或服务主体作为 [GitHub 机密](https://docs.github.com/en/actions/reference/encrypted-secrets) ，以便在 Azure 中进行身份验证。 你将可以访问工作流中的机密。 

# <a name="publish-profile"></a>[发布配置文件](#tab/applevel)

发布配置文件是应用级凭据。 将发布配置文件设置为 GitHub 机密。 

1. 在 Azure 门户中，请参阅应用服务。 

1. 在 " **概述** " 页上，选择 " **获取发布配置文件**"。

1. 保存下载的文件。 你将使用该文件的内容来创建 GitHub 机密。

# <a name="service-principal"></a>[服务主体](#tab/userlevel)

可以在[Azure CLI](/cli/azure/)中使用[az ad sp 创建-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true)命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 使用 Azure 门户中 [Azure Cloud Shell](https://shell.azure.com/) 或选择 " **试用** " 按钮来运行此命令。

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

---

## <a name="configure-the-github-secret"></a>配置 GitHub 机密


# <a name="publish-profile"></a>[发布配置文件](#tab/applevel)

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

若要使用 [应用级凭据](#generate-deployment-credentials)，请将下载的发布配置文件的内容粘贴到机密的值字段中。 命名机密 `AZURE_WEBAPP_PUBLISH_PROFILE` 。

配置 GitHub 工作流时，请 `AZURE_WEBAPP_PUBLISH_PROFILE` 在部署 Azure Web 应用操作中使用。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[服务主体](#tab/userlevel)

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

要使用 [用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>设置环境

可以使用其中一个安装操作来设置环境。

|**语言**  |**设置操作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

以下示例演示了如何为不同的受支持语言设置环境：

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>构建 Web 应用

构建 web 应用并部署到 Azure App Service 更改的过程，具体取决于语言。 

下面的示例显示了以不同支持的语言构建 web 应用的工作流部分。

对于所有语言，你可以将 web 应用根目录设置为 `working-directory` 。 

**.NET**

环境变量 `AZURE_WEBAPP_PACKAGE_PATH` 设置 web 应用项目的路径。 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

您可以还原 NuGet 依赖项并通过运行 msbuild `run` 。 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

对于 Node.js，你可以 `working-directory` 在中设置或更改 npm 目录 `pushd` 。 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>部署到应用服务

若要将代码部署到应用服务应用，请使用 `azure/webapps-deploy@v2` 操作。 此操作具有四个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **app-name** | （必需）应用服务应用的名称 | 
| **publish-profile** | （可选）具有 Web 部署机密的发布配置文件内容 |
| **package** | （可选）包或文件夹的路径。 路径可以包含 * .zip、* war、* .jar 或要部署的文件夹 |
| **slot-name** |  (可选) 输入生产[槽](deploy-staging-slots.md)以外的现有槽 |


# <a name="publish-profile"></a>[发布配置文件](#tab/applevel)

### <a name="net-core"></a>.NET Core

使用 Azure 发布配置文件生成 .NET Core 应用并将其部署到 Azure。 `publish-profile`输入将引用 `AZURE_WEBAPP_PUBLISH_PROFILE` 前面创建的机密。

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

生成并部署使用 NuGet 和身份验证的 ASP.NET MVC 应用 `publish-profile` 。 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

使用 Azure 发布配置文件构建 Java 弹簧应用并将其部署到 Azure。 `publish-profile`输入将引用 `AZURE_WEBAPP_PUBLISH_PROFILE` 前面创建的机密。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

若要部署 `war` 而不是 `jar` ，请更改 `package` 值。 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

使用应用的发布配置文件构建 Node.js 应用并将其部署到 Azure。 `publish-profile`输入将引用 `AZURE_WEBAPP_PUBLISH_PROFILE` 前面创建的机密。

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

使用应用的发布配置文件构建 Python 应用并将其部署到 Azure。 请注意 `publish-profile` 输入如何引用 `AZURE_WEBAPP_PUBLISH_PROFILE` 前面创建的机密。

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2-beta
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[服务主体](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

使用 Azure 服务主体生成 .NET Core 应用并将其部署到 Azure。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

使用 Azure 服务主体生成 ASP.NET MVC 应用并将其部署到 Azure。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

使用 Azure 服务主体生成 Java 弹簧应用并将其部署到 Azure。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

使用 Azure 服务主体构建 Node.js 应用并将其部署到 Azure。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

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
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

使用 Azure 服务主体构建 Python 应用并将其部署到 Azure。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
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
