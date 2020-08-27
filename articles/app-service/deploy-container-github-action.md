---
title: GitHub 操作中的自定义容器 CI/CD
description: 了解如何使用 GitHub 操作通过 CI/CD 管道将自定义 Linux 容器部署到应用服务。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 6af23aba28ce3cda9982878ed08ec515aa25633a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962598"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub 操作将自定义容器部署到应用服务

可以通过 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 灵活地生成自动化软件开发生命周期工作流。 [对于容器的 Azure App Service 操作](https://github.com/Azure/webapps-container-deploy)，可以使用 GitHub 操作自动化工作流，以便部署自定义容器[应用服务](overview.md)。

> [!IMPORTANT]
> GitHub Actions 当前为 beta 版本。 必须首先使用 GitHub 帐户[注册才能加入预览](https://github.com/features/actions)。
> 

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于 Azure App Service 容器工作流，文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体。 <br /> 2. 创建 GitHub 机密。 |
|**生成** | 1. 设置环境。 <br /> 2. 构建容器映像。 |
|**部署** | 1. 部署容器映像。 |

## <a name="create-a-service-principal"></a>创建服务主体

可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 可以使用 Azure 门户中的 [Azure Cloud Shell](https://shell.azure.com/) 或选择“试用”按钮运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

在上面的示例中，将占位符替换为你的订阅 ID 和资源组名称。 输出是一个具有角色分配凭据的 JSON 对象，该对象提供对应用服务应用的访问权限，如下所示。 稍后复制此 JSON 对象。

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
> 始终应授予最小访问权限。 可以在上述 Az CLI 命令中将作用域限制为特定的应用服务应用，并将容器映像推送到 Azure 容器注册表。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

在 [GitHub](https://github.com/)中，浏览存储库，选择 " **设置" > 机密 > 添加新机密**。

将 [创建服务主体](#create-a-service-principal) 的 JSON 输出的内容粘贴为机密变量的值。 为机密指定名称，如 `AZURE_CREDENTIALS` 。

以后配置工作流文件时，请使用机密来输入 `creds` Azure 登录操作。 例如：

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中设置它们。

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>构建容器映像

下面的示例演示生成 docker 映像的工作流的一部分。

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>部署到应用服务容器

若要将映像部署到应用服务中的自定义容器，请使用 `azure/webapps-container-deploy@v1` 操作。 该操作有五个参数：

| **参数**  | **解释**  |
|---------|---------|
| **app-name** | （必需）应用服务应用的名称 | 
| **slot-name** | （可选）输入生产槽以外的现有槽 |
| **images** |  (必需) 指定完全限定的容器映像 () 名称。 例如，"myregistry.azurecr.io/nginx:latest" 或 "python： 3.7.2-alpine/"。 对于多容器应用程序，可以 (多行分隔) 提供多个容器映像名称 |
| **配置-文件** | Docker 合成文件 (可选) 路径。 应为完全限定的路径或相对于默认工作目录。 对于多容器应用是必需的。 |
| **容器-命令** |  (可选) 输入启动命令。 例如， dotnet run 或 dotnet filename.dll |

下面是用于构建 Node.js 应用并将其部署到应用服务中的自定义容器的示例工作流。 请注意 `creds` 输入如何引用 `AZURE_CREDENTIALS` 前面创建的机密。

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

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