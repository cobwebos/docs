---
title: GitHub 操作中的自定义容器 CI/CD
description: 了解如何使用 GitHub 操作通过 CI/CD 管道将自定义 Linux 容器部署到应用服务。
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 3a5e319115c124551c05f2ac5aa393ba19596d0d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893350"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub 操作将自定义容器部署到应用服务

[GitHub 操作](https://help.github.com/en/articles/about-github-actions) 使你可以灵活地生成自动化软件开发工作流。 使用 [Azure Web 部署操作](https://github.com/Azure/webapps-deploy)，可以使用 GitHub 操作自动化工作流，将自定义容器部署到 [应用服务](overview.md) 。

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含工作流中的各个步骤和参数。

对于 Azure App Service 容器工作流，文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 检索服务主体或发布配置文件。 <br /> 2. 创建 GitHub 机密。 |
|**生成** | 1. 创建环境。 <br /> 2. 构建容器映像。 |
|**部署** | 1. 部署容器映像。 |

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 一个 GitHub 帐户。 如果没有，请 [免费](https://github.com/join)注册。  
- 容器的工作容器注册表和 Azure App Service 应用。 此示例使用 Azure 容器注册表。 
    - [了解如何使用 Docker 创建容器化的 Node.js 应用程序，将容器映像推送到注册表，然后将该映像部署到 Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>生成部署凭据

使用 GitHub 操作 Azure 应用服务进行身份验证的建议方法是使用发布配置文件。 你还可以使用服务主体进行身份验证，但该过程需要更多步骤。 

保存你的发布配置文件凭据或服务主体作为 [GitHub 机密](https://docs.github.com/en/actions/reference/encrypted-secrets) ，以便在 Azure 中进行身份验证。 你将可以访问工作流中的机密。 

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

发布配置文件是应用级凭据。 将发布配置文件设置为 GitHub 机密。 

1. 在 Azure 门户中，请参阅应用服务。 

1. 在 " **概述** " 页上，选择 " **获取发布配置文件**"。

1. 保存下载的文件。 你将使用该文件的内容来创建 GitHub 机密。

# <a name="service-principal"></a>[服务主体](#tab/service-principal)

可以在[Azure CLI](/cli/azure/)中使用[az ad sp 创建-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true)命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 使用 Azure 门户中 [Azure Cloud Shell](https://shell.azure.com/) 或选择 " **试用** " 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

在此示例中，将占位符替换为你的订阅 ID、资源组名称和应用名称。 输出是一个 JSON 对象，其中包含用于提供对应用服务应用的访问权限的角色分配凭据。 稍后复制此 JSON 对象。

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

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

将 JSON 输出的内容粘贴为机密变量的值。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>配置用于身份验证的 GitHub 机密

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

若要使用 [应用级凭据](#generate-deployment-credentials)，请将下载的发布配置文件的内容粘贴到机密的值字段中。 命名机密 `AZURE_WEBAPP_PUBLISH_PROFILE` 。

配置 GitHub 工作流时，请 `AZURE_WEBAPP_PUBLISH_PROFILE` 在部署 Azure Web 应用操作中使用。 例如：
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[服务主体](#tab/service-principal)

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

要使用 [用户级凭据](#generate-deployment-credentials)，请将 Azure CLI 命令的整个 JSON 输出粘贴到机密的值字段中。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>为注册表配置 GitHub 机密

定义要用于 Docker 登录操作的机密。 

1. 在 Azure 门户或 Docker 中转到容器，复制用户名和密码。 

2. 为名为的注册表用户名定义新机密 `REGISTRY_USERNAME` 。 

3. 为名为的注册表密码定义新的密码 `REGISTRY_PASSWORD` 。 

## <a name="build-the-container-image"></a>构建容器映像

下面的示例演示生成 Node.JS Docker 映像的工作流的一部分。 使用 [Docker 登录名](https://github.com/azure/docker-login) 登录到专用容器注册表。 此示例使用 Azure 容器注册表，但相同的操作适用于其他注册表。 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

你还可以使用 [Docker 登录名](https://github.com/azure/docker-login) 同时登录多个容器注册表。 此示例包括两个新的 GitHub 机密，用 docker.io 进行身份验证。

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>部署到应用服务容器

若要将映像部署到应用服务中的自定义容器，请使用 `azure/webapps-deploy@v2` 操作。 该操作有五个参数：

| **参数**  | **解释**  |
|---------|---------|
| **app-name** | （必需）应用服务应用的名称 | 
| **publish-profile** | （可选）具有 Web 部署机密的发布配置文件内容 |
| **images** | 完全限定的容器映像 () 名称。 例如，"myregistry.azurecr.io/nginx:latest" 或 "python： 3.7.2-alpine/"。 对于多容器方案，可以 (多行分隔) 提供多个容器映像名称 |
| **slot-name** | （可选）输入生产槽以外的现有槽 |
| **配置-文件** | Docker-Compose 文件 (可选) 路径 |

# <a name="publish-profile"></a>[发布配置文件](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[服务主体](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>后续步骤

可在 GitHub 上找到分组到不同存储库中的操作集，其中每一个都包含文档和示例，帮助你将 GitHub 用于 CI/CD 并将应用部署到 Azure。

- [要部署到 Azure 的操作工作流](https://github.com/Azure/actions-workflow-samples)

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初学者工作流](https://github.com/actions/starter-workflows)