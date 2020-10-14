---
title: 通过 GitHub 操作部署容器实例
description: 配置一个 GitHub 操作，用于自动执行生成容器映像并将其推送和部署到 Azure 容器实例的步骤
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure
ms.openlocfilehash: c01075bcb64aa9b91869daba2e995957da74daf4
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019182"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>配置 GitHub 操作以创建容器实例

[Github 操作](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)是 GitHub 中的一个功能套件，可以在存储代码的同一位置自动执行软件开发工作流，并针对拉取请求和问题进行协作。

使用 " [部署到 Azure 容器实例](https://github.com/azure/aci-deploy) " GitHub 操作可自动将单个容器部署到 Azure 容器实例。 该操作可为容器实例设置属性（类似于在 [az container create][az-container-create] 命令中设置的属性）。

本文介绍了如何在 GitHub 存储库中设置用于执行以下操作的工作流：

* 基于 Dockerfile 生成映像
* 将映像推送到 Azure 容器注册表
* 将容器映像部署到 Azure 容器实例

本文将介绍设置工作流的两种方式：

* [配置 github 工作流](#configure-github-workflow) -使用 "部署到 Azure 容器实例" 操作和其他操作在 GitHub 存储库中创建工作流。  
* [使用 CLI 扩展](#use-deploy-to-azure-extension) -使用 `az container app up` Azure CLI 中的 " [部署到 Azure](https://github.com/Azure/deploy-to-azure-cli-extension) " 扩展中的命令。 此命令简化了 GitHub 工作流的创建和部署步骤。

> [!IMPORTANT]
> 适用于 Azure 容器实例的 GitHub 操作目前为预览版。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* **GitHub 帐户** - 如果没有帐户，请在 https://github.com 上创建一个帐户。
* **Azure CLI** -可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来完成 Azure CLI 步骤。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。
* **Azure 容器注册表** - 如果没有 Azure 容器注册表，请使用 [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)、[Azure 门户](../container-registry/container-registry-get-started-portal.md)或其他方法在基本层中创建一个容器注册表。 记下用于部署的资源组，因为在 GitHub 工作流中需要使用它。

## <a name="set-up-repo"></a>设置存储库

* 对于本文中的示例，请使用 GitHub 创建以下存储库的分支： https://github.com/Azure-Samples/acr-build-helloworld-node

  此存储库包含用来为小型 Web 应用创建容器映像的 Dockerfile 和源文件。

  ![GitHub 中创建分支按钮（突出显示）的屏幕截图](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 确保为你的存储库启用“操作”。 导航到你的分支存储库，并选择“设置” > “操作”。   在“操作权限”中，确保已选中“为此存储库启用本地和第三方操作”。  

## <a name="configure-github-workflow"></a>配置 GitHub 工作流

### <a name="create-service-principal-for-azure-authentication"></a>创建用于 Azure 身份验证的服务主体

在 GitHub 工作流中，需要提供 Azure 凭据，以便在 Azure CLI 中进行身份验证。 以下示例创建一个服务主体，其“参与者”角色作用域限定为你的容器注册表的资源组。

首先获取你的资源组的资源 ID。 请将以下 [az group show][az-group-show] 命令中的占位符替换为你的组名称：

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 创建服务主体：

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

输出类似于：

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

保存 JSON 输出，因为在稍后的步骤中需要使用它。 另请记下 `clientId`，在下一部分更新服务主体时需要使用它。

### <a name="update-service-principal-for-registry-authentication"></a>更新用于注册表身份验证的服务主体

更新 Azure 服务主体凭据，以允许对容器注册表进行推送和请求访问。 此步骤使 GitHub 工作流可以使用服务主体对 [容器注册表进行身份验证](../container-registry/container-registry-auth-service-principal.md) ，并推送和拉取 Docker 映像。 

获取你的容器注册表的资源 ID。 请将以下 [az acr show][az-acr-show] 命令中的占位符替换为你的注册表名称：

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 分配 AcrPush 角色，此角色授予对注册表的推送和拉取访问权限。 替换为你的服务主体的客户端 ID：

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>将凭据保存到 GitHub 存储库

1. 在 GitHub UI 中，导航到你的分支存储库，并选择“设置” > “机密”。   

1. 选择“添加新机密”以添加以下机密： 

|Secret  |Value  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 服务主体创建步骤中的整个 JSON 输出 |
|`REGISTRY_LOGIN_SERVER`   | 注册表的登录服务器名称（全小写）。 示例： *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  创建服务主体后显示的 JSON 输出中的 `clientId`       |
|`REGISTRY_PASSWORD`     |  创建服务主体后显示的 JSON 输出中的 `clientSecret` |
| `RESOURCE_GROUP` | 用来限定服务主体作用域的资源组名称 |

### <a name="create-workflow-file"></a>创建工作流文件

1. 在 GitHub UI 中，选择“操作” > “新建工作流”。  
1. 选择“自己设置工作流”。 
1. 在“编辑新文件”中，粘贴以下 YAML 内容并覆盖示例代码。  接受默认文件名 `main.yml`，或提供你选择的文件名。
1. 选择“开始提交”，并提供你的提交内容的简短或详细说明（可选），然后选择“提交新文件”。  

```yml
on: [push]
name: Linux_Container_Workflow

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
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>验证工作流

提交工作流文件后，会触发该工作流。 若要查看工作流进度，请导航到“操作” > “工作流”。   

![查看工作流进度](./media/container-instances-github-action/github-action-progress.png)

若要了解如何查看工作流中每个步骤的状态和结果，请参阅[管理工作流运行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。 如果工作流未完成，请参阅 [查看日志以诊断失败](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures)。

工作流成功完成后，通过运行[az container show][az-container-show]命令获取名为*sampleapp.exe*的容器实例的相关信息。 替换为你的资源组名称： 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

输出类似于：

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

预配实例后，在浏览器中导航到容器的 FQDN，以查看正在运行的 Web 应用。

![浏览器中正在运行的 Web 应用](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>使用“部署到 Azure”扩展

另外，也可以使用 Azure CLI 中的[“部署到 Azure”扩展](https://github.com/Azure/deploy-to-azure-cli-extension)来配置工作流。 该扩展中的 `az container app up` 命令采用你的输入参数来设置一个工作流，以部署到 Azure 容器实例。 

Azure CLI 创建的工作流类似于可以[使用 GitHub 手动创建](#configure-github-workflow)的工作流。

### <a name="additional-prerequisite"></a>其他先决条件

对于此方案，除了满足[先决条件](#prerequisites)并完成[存储库设置](#set-up-repo)以外，还需要安装 Azure CLI 的 **“部署到 Azure”扩展**。

请运行 [az extension add][az-extension-add] 命令来安装该扩展：

```azurecli
az extension add \
  --name deploy-to-azure
```

有关查找、安装和管理扩展的信息，请参阅[在 Azure CLI 中使用扩展](/cli/azure/azure-cli-extensions-overview)。

### <a name="run-az-container-app-up"></a>运行 `az container app up`

若要运行 [az container app up][az-container-app-up] 命令，请至少提供：

* Azure 容器注册表的名称，例如 *myregistry*
* GitHub 存储库的 URL，例如 `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

示例命令：

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>命令进度

* 出现提示时，请提供 GitHub*凭据，或*提供[github 个人访问令牌](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT) ，其中包含存储库和*用户*范围以使用 GitHub 帐户进行身份验证。 如果提供了 GitHub 凭据，该命令将为你创建一个 PAT。 按照其他提示配置工作流。

* 该命令将为工作流创建存储库机密：

  * 用于 Azure CLI 的服务主体凭据
  * 用于访问 Azure 容器注册表的凭据

* 在该命令将工作流文件提交到存储库后，会触发工作流。 

输出类似于：

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

若要在 GitHub UI 中查看每个步骤的工作流状态和结果，请参阅[管理工作流运行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

### <a name="validate-workflow"></a>验证工作流

工作流使用 GitHub 存储库的基名称（在本例中为 *acr-build-helloworld-node*）部署 Azure 容器实例。 工作流成功完成后，通过运行[az container show][az-container-show]命令获取名为*helloworld-node*的容器实例的相关信息。 替换为你的资源组名称： 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

输出类似于：

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

预配实例后，在浏览器中导航到容器的 FQDN，以查看正在运行的 Web 应用。

## <a name="clean-up-resources"></a>清理资源

使用 [az container delete][az-container-delete] 命令停止容器实例：

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

若要删除资源组及其包含的所有资源，请运行 [az group delete][az-group-delete] 命令：

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>后续步骤

浏览 [GitHub 市场](https://github.com/marketplace?type=actions)，了解用于自动执行开发工作流的其他操作


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
