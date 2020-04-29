---
title: 使用 GitHub Actions 生成和测试容器并将其部署到 Azure Kubernetes 服务
description: 了解如何使用 GitHub Actions 将容器部署到 Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77595359"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>用于将容器部署到 Kubernetes 服务的 GitHub Actions

可以通过 [GitHub Actions](https://help.github.com/en/articles/about-github-actions) 灵活地生成自动化软件开发生命周期工作流。 Kubernetes 操作 [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) 促进到 Azure Kubernetes 服务群集的部署。 此操作设置目标 AKS 群集上下文，该上下文可供其他操作（例如 [azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master)、[azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) 等）使用，也可运行任何 kubectl 命令。

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

对于以 AKS 为目标的工作流，该文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 登录到专用容器注册表 (ACR) |
|**生成** | 生成和推送容器映像  |
|**部署** | 1.设置目标 AKS 群集 |
| |2.在 Kubernetes 群集中创建通用/docker 注册表机密  |
||3.部署到 Kubernetes 群集|

## <a name="create-a-service-principal"></a>创建服务主体

可以在 [Azure CLI](https://docs.microsoft.com/cli/azure/) 中使用 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令创建[服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 你可以使用 Azure 门户中[Azure Cloud Shell](https://shell.azure.com/)或通过选择 "**试用**" 按钮来运行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

在上述命令中，将占位符替换为你的订阅 ID 和资源组。 输出是用于访问资源的角色分配凭据。 此命令应输出下面这样的 JSON 对象。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
请复制此 JSON 对象，它可以用来从 GitHub 进行身份验证。

## <a name="configure-the-github-secrets"></a>配置 GitHub 机密

按照以下步骤配置机密：

1. 在 [GitHub](https://github.com/) 中浏览到存储库，选择“设置”>“机密”>“添加新机密”。****

    ![机密](media/kubernetes-action/secrets.png)

2. 将上述 `az cli` 命令的内容作为机密变量的值粘贴。 例如，`AZURE_CREDENTIALS` 。

3. 同样，为容器注册表凭据定义以下附加机密，并在 Docker 登录操作中设置它们。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 在定义后，会看到如下所示的机密。

    ![kubernetes-secrets](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>生成容器映像并将其部署到 Azure Kubernetes 服务群集

容器映像的生成和推送使用 `Azure/docker-login@v1` 操作完成。 若要将容器映像部署到 AKS，需使用 `Azure/k8s-deploy@v1` 操作。 该操作有五个参数：

| **参数**  | **说明**  |
|---------|---------|
| **namespace** | （可选）选择目标 Kubernetes 命名空间。 如果未提供命名空间，则命令会在默认命名空间中运行 | 
| **进行** |  （必需）将要用于部署的清单文件的路径 |
| **images** | （可选）将要用于在清单文件上进行替换的映像的完全限定资源 URL |
| **imagepullsecrets** | （可选）已在群集中设置的 docker 注册表机密的名称。 这些机密名称的每一个都在输入清单文件中的工作负载的 imagePullSecrets 字段下添加 |
| **kubectl-version** | （可选）安装 kubectl 二进制文件的特定版本 |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes 服务群集

用于生成容器映像并将其部署到 Azure Kubernetes 服务群集的端到端工作流。

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

你可以在 GitHub 上的不同存储库中找到我们的 Actions 集，其中的每一个都包含文档和示例，介绍如何将 GitHub 用于 CI/CD 并将应用部署到 Azure。

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [actions-workflow-samples](https://github.com/Azure/actions-workflow-samples)
