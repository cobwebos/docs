---
title: 来自 GitHub 操作的自定义容器 CI/CD
description: 了解如何使用 GitHub 操作从 CI/CD 管道将自定义 Linux 容器部署到应用服务。
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246968"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>使用 GitHub 操作将自定义容器部署到应用服务

可以通过 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 灵活地生成自动化软件开发生命周期工作流。 使用[容器的 Azure 应用服务操作](https://github.com/Azure/webapps-container-deploy)，可以自动执行工作流，使用 GitHub 操作将应用作为[自定义容器部署到应用服务](https://azure.microsoft.com/services/app-service/containers/)。

> [!IMPORTANT]
> GitHub Actions 目前为 Beta 版。 必须先使用 GitHub 帐户[注册加入预览版](https://github.com/features/actions)。
> 

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

对于 Azure 应用服务容器工作流，该文件有三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 定义服务主体。 <br /> 2. 创建 GitHub 密钥。 |
|**建立** | 1. 设置环境。 <br /> 2. 构建容器映像。 |
|**部署** | 1. 部署容器映像。 |

## <a name="create-a-service-principal"></a>创建服务主体

可以在 [Azure CLI](https://docs.microsoft.com/cli/azure/) 中使用 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 可以使用 Azure 门户中的[Azure 云外壳](https://shell.azure.com/)运行此命令，也可以选择"**试用"** 按钮。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

输出是一个 JSON 对象，具有角色分配凭据，提供对应用服务应用的访问，如下所示。 复制此 JSON 对象以从 GitHub 进行身份验证。

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
> 始终应授予最小访问权限。 您可以将上述 Az CLI 命令中的范围限制为特定的应用服务应用和将容器映像推送到的 Azure 容器注册表。

## <a name="configure-the-github-secret"></a>配置 GitHub 机密

下面的示例使用用户级凭据（即 Azure 服务主体）进行部署。 按照步骤配置机密：

1. 在[GitHub](https://github.com/)中，浏览存储库，选择 **"设置>机密>添加新机密**

2. 将以下`az cli`命令的内容粘贴为机密变量的值。 例如，`AZURE_CREDENTIALS` 。

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 现在，在分支中的工作流文件中：`.github/workflows/workflow.yml`将 Azure 登录操作中的机密替换为机密。

4. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中设置它们。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 定义后，您将看到如下所示的机密。

    ![容器机密](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>生成容器映像

下面的示例显示生成 docker 映像的工作流的一部分。

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

要将映像部署到应用服务中的自定义容器，请使用 操作`azure/webapps-container-deploy@v1`。 该操作有五个参数：

| **参数**  | **说明**  |
|---------|---------|
| **应用名称** | （必需）应用服务应用的名称 | 
| **插槽名称** | （可选）输入生产槽以外的现有插槽 |
| **图像** | （必需）指定完全限定的容器映像名称。 例如，"myregistry.azurecr.io/nginx:latest"或"python：3.7.2-高山/"。 对于多容器应用，可以提供多个容器映像名称（多行分隔） |
| **配置文件** | （可选）Docker-Compose 文件的路径。 应是完全限定的路径或相对于默认工作目录。 多容器应用需要。 |
| **容器命令** | （可选）输入启动命令。 例如， 点网运行或点网文件名.dll |

下面是生成 Node.js 应用并将其部署到应用服务中的自定义容器的示例工作流。

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

您可以在 GitHub 上找到我们分组到不同存储库的操作集，每个存储库都包含文档和示例，以帮助您使用 GitHub 进行 CI/CD 并将应用部署到 Azure。

- [Azure 登录](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [用于容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登录/注销](https://github.com/Azure/docker-login)

- [触发工作流的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [初学者 CI 工作流](https://github.com/actions/starter-workflows)

- [要部署到 Azure 的初学者工作流](https://github.com/Azure/actions-workflow-samples)
