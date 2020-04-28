---
title: 按 GitHub 操作部署容器实例
description: 配置一个 GitHub 操作，以自动执行创建、推送容器映像并将其部署到 Azure 容器实例的步骤
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80258033"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>配置 GitHub 操作以创建容器实例

[Github 操作](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)是 github 中的一套功能，可用于在存储代码的同一位置自动执行软件开发工作流，以及对拉取请求和问题进行协作。

使用 "[部署到 Azure 容器实例](https://github.com/azure/aci-deploy)" GitHub 操作可自动将容器部署到 Azure 容器实例。 操作允许您为容器实例设置类似于[az container create][az-container-create]命令中的属性。

本文介绍如何在 GitHub 存储库中设置执行以下操作的工作流：

* 基于 Dockerfile 生成映像
* 将映像推送到 Azure 容器注册表
* 将容器映像部署到 Azure 容器实例

本文介绍了设置工作流的两种方法：

* 使用 "部署到 Azure 容器实例" 操作和其他操作，亲自在 GitHub 存储库中配置工作流。  
* 使用 Azure CLI `az container app up`中的 "[部署到 Azure](https://github.com/Azure/deploy-to-azure-cli-extension) " 扩展中的命令。 此命令简化了 GitHub 工作流和部署步骤的创建。

> [!IMPORTANT]
> Azure 容器实例的 GitHub 操作当前为预览版。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="prerequisites"></a>先决条件

* **GitHub 帐户**- https://github.com如果你还没有帐户，则创建一个。
* **Azure CLI** -可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来完成 Azure CLI 步骤。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。
* **Azure 容器注册表**-如果没有，请使用[Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)、 [Azure 门户](../container-registry/container-registry-get-started-portal.md)或其他方法在基本层中创建 azure 容器注册表。 记下用于部署的资源组，该资源组用于 GitHub 工作流。

## <a name="set-up-repo"></a>设置存储库

* 对于本文中的示例，请使用 GitHub 分叉以下存储库：https://github.com/Azure-Samples/acr-build-helloworld-node

  此存储库包含用于创建小型 web 应用容器映像的 Dockerfile 和源文件。

  ![GitHub 中创建分支按钮（突出显示）的屏幕截图](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 确保为存储库启用了操作。 导航到分叉存储库，然后选择 "**设置** > " "**操作**"。 在 "**操作权限**" 中，确保已选中 "**为此存储库启用本地和第三方操作**"。

## <a name="configure-github-workflow"></a>配置 GitHub 工作流

### <a name="create-service-principal-for-azure-authentication"></a>创建 Azure 身份验证的服务主体

在 GitHub 工作流中，需要提供 Azure 凭据以对 Azure CLI 进行身份验证。 以下示例创建一个服务主体，其中的参与者角色范围为容器注册表的资源组。

首先，获取资源组的资源 ID。 用以下[az group show][az-acr-show]命令替换组名称：

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

使用[az ad sp 创建-rbac][az-ad-sp-create-for-rbac]创建服务主体：

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

保存 JSON 输出，因为后面的步骤中会用到它。 另外，请记下`clientId`，需要在下一部分更新服务主体。

### <a name="update-service-principal-for-registry-authentication"></a>更新注册表身份验证的服务主体

更新 Azure 服务主体凭据，以允许对容器注册表进行推送和拉取。 此步骤允许 GitHub 工作流使用服务主体对[容器注册表进行身份验证](../container-registry/container-registry-auth-service-principal.md)。 

获取容器注册表的资源 ID。 在下面的[az acr show][az-acr-show]命令中替换注册表的名称：

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

使用[az role 赋值 create][az-role-assignment-create]来分配 AcrPush 角色，该角色提供对注册表的推送和拉取访问权限。 替换服务主体的客户端 ID：

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>将凭据保存到 GitHub 存储库

1. 在 GitHub UI 中，导航到分叉的存储库，并选择 "**设置** > " "**机密**"。 

1. 选择 "**添加新机密**" 添加以下机密：

|Secret  |值  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 从服务主体创建的整个 JSON 输出 |
|`REGISTRY_LOGIN_SERVER`   | 注册表的登录服务器名称（全部小写）。 示例： *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  从`clientId`服务主体创建的 JSON 输出中的       |
|`REGISTRY_PASSWORD`     |  从`clientSecret`服务主体创建的 JSON 输出中的 |
| `RESOURCE_GROUP` | 用于确定服务主体范围的资源组的名称 |

### <a name="create-workflow-file"></a>创建工作流文件

1. 在 GitHub UI 中，选择 "**操作** > " "**新建工作流**"。
1. 选择 "**自行设置工作流**"。
1. 在 "**编辑新文件**" 中，粘贴以下 YAML 内容以覆盖示例代码。 接受默认文件名`main.yml`，或者提供您选择的文件名。
1. 选择 "**启动提交**"，还可以选择提供提交的简短说明，然后选择 "**提交新文件**"。

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

提交工作流文件后，将触发工作流。 若要查看工作流进度，请导航到 "**操作** > **工作流**"。 

![查看工作流进度](./media/container-instances-github-action/github-action-progress.png)

有关查看工作流中每个步骤的状态和结果的信息，请参阅[管理工作流运行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

工作流完成时，通过运行[az container show][az-container-show]命令获取名为*sampleapp.exe*的容器实例的相关信息。 替换资源组的名称： 

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

预配实例后，在浏览器中导航到容器的 FQDN，查看正在运行的 web 应用。

![在浏览器中运行 web 应用](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>使用 "部署到 Azure" 扩展

或者，使用 Azure CLI 中的 "[部署到 Azure" 扩展](https://github.com/Azure/deploy-to-azure-cli-extension)来配置工作流。 扩展`az container app up`中的命令采用输入参数来设置要部署到 Azure 容器实例的工作流。 

Azure CLI 创建的工作流类似于可以[使用 GitHub 手动创建](#configure-github-workflow)的工作流。

### <a name="additional-prerequisite"></a>其他先决条件

除了针对此方案的[先决条件](#prerequisites)和存储库[设置](#set-up-repo)外，还需要为 Azure CLI 安装 "**部署到 Azure" 扩展**。

运行[az extension add][az-extension-add]命令以安装扩展：

```azurecli
az extension add \
  --name deploy-to-azure
```

有关查找、安装和管理扩展的信息，请参阅[将扩展用于 Azure CLI](/cli/azure/azure-cli-extensions-overview)。

### <a name="run-az-container-app-up"></a>`az container app up`运行

若要运行[az container app up][az-container-app-up]命令，请至少提供：

* Azure 容器注册表的名称，例如， *myregistry*
* GitHub 存储库的 URL，例如`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

示例命令：

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>命令进度

* 出现提示时，请提供 GitHub*凭据，或*提供[github 个人访问令牌](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)（PAT），其中包含用于在注册表中进行身份验证的存储库和*用户*作用域。 如果提供 GitHub 凭据，则该命令会为你创建一个 PAT。

* 命令为工作流创建存储库机密：

  * Azure CLI 的服务主体凭据
  * 用于访问 Azure 容器注册表的凭据

* 命令将工作流文件提交到存储库后，将触发工作流。 

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

### <a name="validate-workflow"></a>验证工作流

工作流使用 GitHub 存储库的基名称（在此例中为 " *acr-helloworld-节点*"）部署 Azure 容器实例。 在浏览器中，可以浏览到提供的链接来查看正在运行的 web 应用。 如果你的应用程序在8080以外的其他端口上进行侦听，请在 URL 中指定该端口。

若要查看 GitHub UI 中每个步骤的工作流状态和结果，请参阅[管理工作流运行](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)。

## <a name="clean-up-resources"></a>清理资源

使用 [az container delete][az-container-delete] 命令停止容器实例：

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

若要删除资源组及其所有资源，请运行[az group delete][az-group-delete]命令：

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>后续步骤

浏览[GitHub Marketplace](https://github.com/marketplace?type=actions)以获取用于自动执行开发工作流的更多操作


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
