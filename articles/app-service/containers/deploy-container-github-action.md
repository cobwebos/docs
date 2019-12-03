---
title: GitHub 操作中的自定义容器 CI/CD
description: 了解如何使用 GitHub 操作通过 CI/CD 管道将自定义 Linux 容器部署到应用服务。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 127dd8645596b605980bf3c6fbc87bf159f7c03e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671808"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub 操作将自定义容器部署到应用服务

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您可以灵活地生成自动软件开发生命周期工作流。 [对于容器的 Azure App Service 操作](https://github.com/Azure/webapps-container-deploy)，可以使用 GitHub 操作自动化工作流，以便将应用作为[自定义容器部署到应用服务](https://azure.microsoft.com/services/app-service/containers/)。

> [!IMPORTANT]
> GitHub 操作当前为 beta 版本。 必须首先[注册，才能](https://github.com/features/actions)使用 GitHub 帐户加入预览版。
> 

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于 Azure App Service 容器工作流，文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体。 <br /> 2. 创建 GitHub 机密。 |
|**生成** | 1. 设置环境。 <br /> 2. 构建容器映像。 |
|**部署** | 1. 部署容器映像。 |

## <a name="create-a-service-principal"></a>创建服务主体

你可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 创建-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令来创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com/)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

输出是一个具有角色分配凭据的 JSON 对象，该对象提供对应用服务应用的访问权限，如下所示。 复制此 JSON 对象以便从 GitHub 进行身份验证。

 ```azurecli 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 授予最小访问权限始终是一种很好的做法。 可以在上述 Az CLI 命令中将作用域限制为特定的应用服务应用，并将容器映像推送到 Azure 容器注册表。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

下面的示例使用用户级凭据，即用于部署的 Azure 服务主体。 按照以下步骤配置机密：

1. 在[GitHub](https://github.com/)中，浏览存储库，选择 "**设置" > 机密 "> 添加新密钥**

2. 将以下 `az cli` 命令的内容粘贴为 "机密变量" 的值。 例如，`AZURE_CREDENTIALS` 。

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 现在，在分支中的工作流文件中： `.github/workflows/workflow.yml` 将 Azure 登录操作中的机密替换为机密。

4. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中进行设置。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 你会看到如下所示的机密。

    ![容器机密](../media/app-service-github-actions/app-service-secrets-container.png)

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
    - name: 'Checkout Github Action' 
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

若要将映像部署到应用服务中的自定义容器，请使用 `azure/webapps-container-deploy@v1` 操作。 此操作有五个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **应用名称** | 请求应用服务应用的名称 | 
| **槽名称** | 可有可无输入生产槽以外的现有槽 |
| **images** | 请求指定完全限定的容器映像名称。 例如，"myregistry.azurecr.io/nginx:latest" 或 "python： 3.7.2-alpine/"。 对于多容器应用程序，可以提供多个容器映像名称（多行分隔） |
| **配置-文件** | 可有可无Docker 合成文件的路径。 应为完全限定的路径或相对于默认工作目录。 对于多容器应用是必需的。 |
| **容器-命令** | 可有可无输入启动命令。 对于 ex dotnet run 或 dotnet filename .dll |

下面是用于生成 node.js 应用并将其部署到应用服务中的自定义容器的示例工作流。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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

可在 GitHub 上找到一组分组到不同存储库中的操作，其中每个操作都包含文档和示例，帮助你将 GitHub 用于 CI/CD 并将你的应用部署到 Azure。

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp for 容器](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [Starter CI 工作流](https://github.com/actions/starter-workflows)

- [要部署到 Azure 的 Starter 工作流](https://github.com/Azure/actions-workflow-samples)
