---
title: 教程 - 使用 Azure 容器注册表生成在云中生成容器映像
description: 本教程介绍如何使用 Azure 容器注册表生成（ACR 生成）在 Azure 中生成 Docker 容器映像，然后将其部署到 Azure 容器实例。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>教程：使用 Azure 容器注册表生成在云中生成容器映像

“ACR 生成”是 Azure 容器注册表中的功能套件，用于在 Azure 中以简化、高效的方式生成 Docker 容器映像。 本文介绍如何使用 ACR 生成的快速生成功能。 快速生成可将开发“内循环”扩展到云中，同时提供成功生成验证并自动将成功生成的映像推送到容器注册表。 映像将在云中本机生成，其位置靠近注册表，可加快部署。

所有 Dockerfile 专业知识都可以直接转移到 ACR 生成。 要通过 ACR 生成在云中生成，无需更改 Dockerfile，只需运行命令。

本教程是系列教程的第一部分，将介绍：

> [!div class="checklist"]
> * 获取示例应用程序源代码
> * 在 Azure 中生成容器映像
> * 将容器部署到 Azure 容器实例

在后续教程中，可了解如何使用 ACR 生成的生成任务在代码提交和基础映像更新时自动生成容器映像。

> [!IMPORTANT]
> 目前，ACR 生成为预览版，仅在美国东部 (eastus) 和欧洲西部 (westeurope) 区域的 Azure 容器注册表中受支持。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想在本地使用 Azure CLI，必须安装 2.0.32 或更高版本的 Azure CLI。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI 2.0][azure-cli]。

## <a name="prerequisites"></a>先决条件

### <a name="github-account"></a>GitHub 帐户

在 https://github.com 上创建帐户（如果还没有帐户）。 本系列教程使用 GitHub 存储库来演示 ACR 生成中的自动映像生成。

### <a name="fork-sample-repository"></a>创建示例存储库分支

接下来，使用 GitHub UI 在 GitHub 帐户中创建示例存储库分支。 本教程从存储库的源生成容器映像，下一教程会将提交推送到存储库的分支以启动自动生成。

创建此存储库的分支：https://github.com/Azure-Samples/acr-build-helloworld-node

![GitHub 中创建分支按钮（突出显示）的屏幕截图][quick-build-01-fork]

### <a name="clone-your-fork"></a>克隆分支

创建存储库分支后，克隆分支然后输入包含本地克隆的目录。

使用 `git` 克隆存储库，将“\<your-github-username\>”替换为你的 GitHub 用户名：

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

输入包含源代码的目录：

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash shell

本系列教程中的命令设置为 Bash Shell 的格式。 如果更希望使用 PowerShell、命令提示符或另一种 shell，可能需要相应地调整行延续和环境变量格式。

## <a name="build-in-azure-with-acr-build"></a>使用 ACR 生成在 Azure 中生成

现已将源代码拉取到计算机中，请执行以下步骤来创建容器注册表并使用 ACR 生成生成容器映像。

为使执行示例命令更轻松，本系列教程使用 shell 环境变量。 执行以下命令来设置 `ACR_NAME` 变量。 将“\<registry-name\>”替换为新容器注册表的唯一名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 本教程中创建的其他资源都基于该名称，因此仅需要修改该第一个变量。

```azurecli-interactive
ACR_NAME=<registry-name>
```

填充容器注册表环境变量后，现在应能够复制并粘贴本教程中的其余命令，并且无需编辑任何值。 执行以下命令来创建资源组和容器注册表：

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

现已拥有注册表，请使用 ACR 生成从示例代码生成容器映像。 执行 [az acr build][az-acr-build] 命令以执行快速生成：

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

[az acr build][az-acr-build] 命令的输出类似于以下示例。 可看到源代码（“上下文”）上传到 Azure 和 ACR 生成在云中运行的 `docker build` 操作的详细信息。 由于 ACR 生成使用 `docker build` 生成映像，因此无需对 Dockerfile 进行任何更改即可立即开始使用 ACR 生成，。

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

在接近输出末尾的位置，ACR 生成显示其为映像找到的依赖项。 这使 ACR 生成可在基础映像更新（如基础映像基于 OS 或框架补丁进行更新）时自动化映像生成。 该系列教程稍后会介绍针对基础映像更新的 ACR 生成支持。

## <a name="deploy-to-azure-container-instances"></a>部署到 Azure 容器实例

ACR 生成默认将成功生成的映像自动推送到注册表，这样即可立即从注册表部署这些映像。

本部分将创建 Azure Key Vault 和服务主体，然后使用服务主体的凭据将容器部署到 Azure 容器实例 (ACI)。

### <a name="configure-registry-authentication"></a>配置注册表身份验证

所有生产方案都应使用[服务主体][service-principal-auth]访问 Azure 容器注册表。 使用服务主体可以提供对容器映像的基于角色的访问控制。 例如，可将服务主体配置为拥有注册表的仅限提取的访问权限。

#### <a name="create-key-vault"></a>创建 Key Vault

如果 [Azure Key Vault](/azure/key-vault/) 中没有保管库，请在 Azure CLI 中使用以下命令创建一个保管库。

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>创建服务主体并存储凭据

现在需要创建服务主体，并将其凭据存储在 Key Vault 中。

请使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令创建服务主体，使用 [az keyvault secret set][az-keyvault-secret-set] 将服务主体的密码存储在保管库中：

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

上述命令中的 `--role` 参数配置具有“读取者”角色的服务主体，该服务主体对注册表拥有仅限提取的访问权限。 若要授予推送和提取访问权限，请将 `--role` 参数更改为 *contributor*。

接下来，将服务主体的 appId（传递给 Azure 容器注册表用于身份验证的“用户名”）存储在保管库中：

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

现已创建 Azure Key Vault 并在其中存储了两个机密：

* `$ACR_NAME-pull-usr`：用作容器注册表**用户名**的服务主体 ID。
* `$ACR_NAME-pull-pwd`：用作容器注册表**密码**的服务主体密码。

现在，当你或你的应用程序和服务从注册表提取映像时，可以按名称引用这些机密。

### <a name="deploy-container-with-azure-cli"></a>使用 Azure CLI 部署容器

将服务主体凭据作为 Azure Key Vault 机密存储后，应用程序和服务可以使用它们来访问专用注册表。

执行以下 [az container create][az-container-create] 命令来部署容器实例。 该命令使用 Azure Key Vault 中存储的服务主体凭据对容器注册表进行身份验证。

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

`--dns-name-label` 值必须在 Azure 中唯一，因此，上述命令会将容器注册表名称追加到容器的 DNS 名称标签。 该命令的输出显示容器的完全限定域名 (FQDN)，例如：

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

记下容器的 FQDN，后续部分将会用到它。

### <a name="verify-deployment"></a>验证部署

若要观看容器的启动过程，请使用 [az container attach][az-container-attach] 命令：

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

`az container attach` 输出在拉取映像和启动时会首先显示容器状态，然后将本地控制台的 STDOUT 和 STDERR 绑定到容器的 STDOUT 和 STDERR。

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

出现 `Server running at http://localhost:80` 时，在浏览器中导航到容器的 FQDN 以查看正在运行的应用程序：

![浏览器中呈现示例应用程序的屏幕截图][quick-build-02-browser]

若要从容器拆离控制台，请按 `Control+C`。

## <a name="clean-up-resources"></a>清理资源

使用 [az container delete][az-container-delete] 命令停止容器实例：

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

若要删除本教程中创建的所有资源，包括容器注册表、密钥保管库和服务主体，请运行以下命令。 但是，本系列的[下一个教程](container-registry-tutorial-build-task.md)也会使用这些资源，因此，如果直接前往下一个教程，则可以保留这些资源。

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>后续步骤

现已使用快速生成测试了内循环，请在将源代码提交到 Git 存储库时，配置“生成任务”以触发容器映像生成：

> [!div class="nextstepaction"]
> [使用生成任务触发自动生成](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
