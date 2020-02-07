---
title: 使用 GitHub 操作生成、测试容器并将其部署到 Azure Kubernetes 服务
description: 了解如何使用 GitHub 操作将容器部署到 Kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 62fcdf01250728cf84726db7e9b39452a4d4e5ff
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046347"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>用于部署到 Kubernetes 服务的 GitHub 操作

[GitHub 操作](https://help.github.com/en/articles/about-github-actions)使您可以灵活地生成自动软件开发生命周期工作流。 Kubernetes 操作[azure/aks-set-context@v1](https://github.com/Azure/aks-set-context)有助于 Azure Kubernetes Service 群集的部署。 操作将设置目标 AKS 群集上下文，该上下文可被其他操作（如[azure/k8s](https://github.com/Azure/k8s-deploy/tree/master)、 [azure/k8s](https://github.com/Azure/k8s-create-secret/tree/master)等）使用或运行任何 kubectl 命令。

工作流是由存储库中 `/.github/workflows/` 路径中的 YAML （docker-compose.override.yml）文件定义的。 此定义包含组成工作流的各种步骤和参数。

对于面向 AKS 的工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 登录到专用容器注册表（ACR） |
|**生成** | 生成 & 推送容器映像  |
|**部署** | 1. 设置目标 AKS 群集 |
| |2. 在 Kubernetes 群集中创建通用/docker 注册表机密  |
||3. 部署到 Kubernetes 群集|

## <a name="create-a-service-principal"></a>创建服务主体

你可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp 创建-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令来创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com/)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，将占位符替换为你的订阅 ID 和资源组。 输出是提供资源访问权限的角色分配凭据。 此命令应输出类似于下面的 JSON 对象。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
复制此 JSON 对象，该对象可用于从 GitHub 进行身份验证。

## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

按照以下步骤配置机密：

1. 在[GitHub](https://github.com/)中，浏览到存储库，选择 "**设置" > 机密 "> 添加新机密**。

    ![机密](media/kubernetes-action/secrets.png)

2. 将上述 `az cli` 命令的内容粘贴为 secret 变量的值。 例如，`AZURE_CREDENTIALS` 。

3. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中进行设置。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 在定义后，你将看到如下所示的机密。

    ![kubernetes-机密](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>构建容器映像并将其部署到 Azure Kubernetes Service 群集

容器映像的生成和推送使用 `Azure/docker-login@v1` 操作完成。 若要将容器映像部署到 AKS，需要使用 `Azure/k8s-deploy@v1` 操作。 此操作有五个参数：

| **Parameter**  | **解释**  |
|---------|---------|
| **名称** | 可有可无选择目标 Kubernetes 命名空间。 如果未提供命名空间，则命令将在默认命名空间中运行 | 
| **进行** |  请求将用于部署的清单文件的路径 |
| **images** | 可有可无要用于在清单文件上进行替换的图像的完全限定资源 URL |
| **imagepullsecrets** | 可有可无已在群集中设置的 docker 注册表机密的名称。 在输入清单文件中找到的工作负荷的 imagePullSecrets 字段下添加这些机密名称 |
| **kubectl-版本** | 可有可无安装 kubectl 二进制文件的特定版本 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes Service 群集

用于构建容器映像和部署到 Azure Kubernetes Service 群集的端到端工作流。

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>后续步骤

你可以在 GitHub 上的不同存储库中找到一组操作，其中每个操作都包含文档和示例，以帮助你将 GitHub 用于 CI/CD 并将你的应用部署到 Azure。

- [安装程序-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-上下文](https://github.com/Azure/k8s-set-context)

- [aks-上下文](https://github.com/Azure/aks-set-context)

- [k8s](https://github.com/Azure/k8s-create-secret)

- [k8s-部署](https://github.com/Azure/k8s-deploy)

- [webapps-部署](https://github.com/Azure/webapps-container-deploy)

- [操作-工作流-示例](https://github.com/Azure/actions-workflow-samples)
