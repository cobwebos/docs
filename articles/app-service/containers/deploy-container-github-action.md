---
title: 使用 GitHub 操作从 CI/CD 管道部署容器-Azure App Service |Microsoft Docs
description: 了解如何使用 GitHub 操作将容器部署到应用服务
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
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809807"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>用于部署到用于容器的 Web 应用的 GitHub 操作

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您可以灵活地生成自动软件开发生命周期工作流。 使用 GitHub Azure App Service 操作时，可以使用 GitHub 操作自动化工作流，以便为[容器部署 Azure Web 应用](https://azure.microsoft.com/services/app-service/containers/)。

> [!IMPORTANT]
> GitHub 操作当前为 beta 版本。 必须首先[注册，才能](https://github.com/features/actions)使用 GitHub 帐户加入预览版。
> 

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于 Azure Web 应用容器工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体 <br /> 2. 创建 GitHub 机密 |
|**生成** | 1. 设置环境 <br /> 2. 构建容器映像 |
|**部署** | 1. 部署容器映像 |

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

5. 在定义后，你将看到如下所示的机密。

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
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>部署到 Web 应用容器

若要将映像部署到 web 应用容器，需要使用 `Azure/appservice-actions/webapp@master` 操作。 此操作有5个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **应用名称** | 请求Azure Web 应用的名称 | 
| **槽名称** | 可有可无输入生产槽以外的现有槽 |
| **images** | 请求指定完全限定的容器映像名称。 例如，"myregistry.azurecr.io/nginx:latest" 或 "python： 3.7.2-alpine/"。 对于多容器方案，可以提供多个容器映像名称（多行分隔） |
| **配置-文件** | 可有可无Docker 合成文件的路径。 应为完全限定的路径或相对于默认工作目录。 对于多容器方案是必需的 |
| **容器-命令** | 可有可无输入 "启动" 命令。 对于 ex dotnet run 或 dotnet filename .dll |

下面是用于构建 node.js Web 应用并将其部署到 Azure Web 应用容器的示例工作流。

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
      uses: azure/actions/login@v1
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

- [Azure 登录](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp for 容器](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [Starter 工作流](https://github.com/actions/starter-workflows)
