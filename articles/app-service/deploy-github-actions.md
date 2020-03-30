---
title: 使用 GitHub 操作配置 CI/CD
description: 了解如何使用 GitHub 操作从 CI/CD 管道将代码部署到 Azure 应用服务。 自定义生成任务并执行复杂的部署。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 4a8b3cf47235e061e5dbcc08a409fce84d421771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562201"
---
# <a name="deploy-to-app-service-using-github-actions"></a>使用 GitHub 操作部署到应用服务

可以通过 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 灵活地生成自动化软件开发生命周期工作流。 使用 GitHub 的 Azure 应用服务操作，可以自动执行工作流以使用 GitHub 操作部署到[Azure 应用服务](overview.md)。

> [!IMPORTANT]
> GitHub Actions 目前为 Beta 版。 必须先使用 GitHub 帐户[注册加入预览版](https://github.com/features/actions)。
> 

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

对于 Azure 应用服务工作流，该文件有三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体 <br /> 2. 创建 GitHub 机密 |
|**建立** | 1. 设置环境 <br /> 2. 构建 Web 应用程序 |
|**部署** | 1. 部署 Web 应用 |

## <a name="create-a-service-principal"></a>创建服务主体

可以在 [Azure CLI](https://docs.microsoft.com/cli/azure/) 中使用 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 门户中的[Azure 云外壳](https://shell.azure.com/)运行此命令，也可以选择"**试用"** 按钮。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

在此示例中，将资源中的占位符替换为订阅 ID、资源组名称和应用名称。 输出是提供对应用服务应用访问权限的角色分配凭据。 请复制此 JSON 对象，它可以用来从 GitHub 进行身份验证。

> [!NOTE]
> 如果您决定使用发布配置文件进行身份验证，则无需创建服务主体。

> [!IMPORTANT]
> 始终应授予最小访问权限。 这就是为什么上一个示例中的范围仅限于特定的应用服务应用，而不是整个资源组。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

您还可以使用应用级凭据（即发布配置文件进行部署）。 按照步骤配置机密：

1. 使用 **"获取发布配置文件"** 选项从门户下载应用服务应用的发布配置文件。

2. 在[GitHub](https://github.com/)中，浏览存储库，选择 **"设置>机密>添加新机密**

    ![机密](media/app-service-github-actions/secrets.png)

3. 将下载的发布配置文件文件的内容粘贴到机密的值字段中。

4. 现在，在分支中的工作流文件中：`.github/workflows/workflow.yml`替换部署 Azure Web`publish-profile`应用操作的输入的机密。
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 定义后，您将看到如下所示的机密。

    ![机密](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>设置环境

可以使用其中一个设置操作来设置环境。

|**语言**  |**设置操作**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**Javascript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

以下示例显示用于为各种受支持的语言设置环境的部分工作流：

**Javascript**

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

这取决于 Azure 应用服务支持的语言和语言，此部分应该是每种语言的标准生成步骤。

以下示例以各种受支持的语言显示构建 Web 应用的工作流部分。

**Javascript**

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

要将代码部署到应用服务应用，请使用 操作`azure/webapps-deploy@v1 `。 此操作有四个参数：

| **参数**  | **说明**  |
|---------|---------|
| **应用名称** | （必需）应用服务应用的名称 | 
| **发布配置文件** | （可选）使用 Web 部署机密发布配置文件内容 |
| **包** | （可选）包或文件夹的路径。 *.zip、*.war、*.jar 或要部署的文件夹 |
| **插槽名称** | （可选）输入生产槽以外的现有插槽 |

### <a name="deploy-using-publish-profile"></a>使用发布配置文件进行部署

下面是使用发布配置文件生成 Node.js 应用并将其部署到 Azure 的示例工作流。

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
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>使用 Azure 服务主体进行部署

下面是使用 Azure 服务主体生成 Node.js 应用并将其部署到 Azure 的示例工作流。

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
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>后续步骤

您可以在 GitHub 上找到我们分组到不同存储库的操作集，每个存储库都包含文档和示例，以帮助您使用 GitHub 进行 CI/CD 并将应用部署到 Azure。

- [要部署到 Azure 的操作工作流](https://github.com/Azure/actions-workflow-samples)

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [用于容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初学者工作流](https://github.com/actions/starter-workflows)
