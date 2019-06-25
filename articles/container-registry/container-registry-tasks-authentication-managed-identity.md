---
title: 使用 Azure 容器注册表任务使用托管的标识
description: 提供对 Azure 资源包括通过 Azure 资源的分配的托管的标识的其他专用容器注册表的 Azure 容器注册表任务访问权限。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148025"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>使用 Azure 托管 ACR 任务中的标识 

使用[管理 Azure 资源的标识](../active-directory/managed-identities-azure-resources/overview.md)进行身份验证从 ACR 任务到 Azure 容器注册表或其他 Azure 资源，而无需提供或管理在代码中的凭据。 例如，使用托管的标识或拉取容器映像推送到另一个注册表，作为一项任务中的步骤。

在本文中，您了解有关管理的标识的详细信息和操作方法：

> [!div class="checklist"]
> * 启用系统分配标识或上一个 ACR 任务的用户分配标识
> * 授予对 Azure 资源，例如其他 Azure 容器注册表的标识访问权限
> * 使用托管的标识从任务访问的资源 

若要创建的 Azure 资源，本文要求运行 Azure CLI 版本 2.0.66 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识可在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管标识。 你可以配置[某些 Azure 资源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)，ACR 任务包括托管标识。 然后使用该标识访问其他 Azure 资源，而无需在代码或脚本中传递凭据。

托管标识有两种类型：

* *用户分配的标识*，可以将其分配给多个资源，并根据需要持久保存。 用户分配的标识现提供预览版。

* 一个*系统托管标识*，这是唯一的某个特定的资源，例如 ACR 任务，该资源的生存期内持续。

设置托管标识的 Azure 资源后，授予对另一个资源，就像任何安全主体的标识访问权限。 例如，将分配的托管的标识具有拉取、 推送和拉取或其他权限的角色到专用容器注册表在 Azure 中。 （有关完整的注册表角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。）可以授予标识对一个或多个资源的访问权限。

## <a name="create-container-registries"></a>创建容器注册表

对于本教程中，你将需要三个容器注册表：

* 使用第一个注册表创建和执行 ACR 任务。 在本文中，名为此源注册表*myregistry*。 
* 第二个和第三个注册表是要推送的映像生成的第一个示例任务的目标注册表。 在本文中，目标注册表名为*customregistry1*并*customregistry2*。

替换为自己的注册表名称在后续步骤中。

如果您没有所需的 Azure 容器注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 不需要尚未向注册表推送映像。

## <a name="example-task-with-a-system-assigned-identity"></a>示例：使用系统分配标识的任务

此示例演示如何创建[多步骤任务](container-registry-tasks-multi-step.md)具有系统分配的标识。 任务生成映像，然后使用标识来使用两个目标注册表将映像推送进行身份验证。

此示例中任务的步骤中定义[YAML 文件](container-registry-tasks-reference-yaml.md)名为`testtask.yaml`。 该文件位于的 multipleRegistries 目录[acr 任务](https://github.com/Azure-Samples/acr-tasks)示例存储库。 该文件将在此处重现：

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>使用系统分配的标识创建任务

创建任务*多个 reg*通过执行以下[az acr 任务创建][ az-acr-task-create]命令。 任务上下文是 multipleRegistries 文件夹的示例存储库，并且该命令引用的文件`testtask.yaml`存储库中。 `--assign-identity`带任何附加值的参数创建该任务的系统分配标识。 此任务是设置，因此您必须手动触发，但您可将它设置到存储库推送提交或拉取请求时运行。 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

在命令输出中，`identity`部分显示的类型标识`SystemAssigned`任务中设置。 `principalId`是标识的服务主体 ID:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

使用[az acr 任务 show] [ az-acr-task-show]命令存储`principalId`在变量中，若要在更高版本的命令中使用：

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>授予对两个目标容器注册表的标识请求权限

在本部分中，若要将推送到两个目标注册表，名为的系统分配的标识权限授予*customregistry1*并*customregistry2*。

首先，使用[az acr show] [ az-acr-show]命令来获取每个注册表的资源 ID 并将 Id 存储在变量中：

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

使用[创建的 az 角色分配][ az-role-assignment-create]命令来分配标识`acrpush`到每个注册表的角色。 此角色具有到容器注册表拉取和推送映像的权限。

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>将目标注册表凭据添加到任务

现在，使用[az acr 任务凭据添加][ az-acr-task-credential-add]命令，将标识的凭据添加到任务，以便它能够与这两个目标注册表身份验证。

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>手动运行任务

使用[az acr 任务运行][ az-acr-task-run]命令以手动触发的任务。 `--set`参数用于将作为任务变量的值的两个目标注册表登录服务器名称中传递`REGISTRY1`和`REGISTRY2`。

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

输出类似于：

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>示例：使用用户分配标识的任务

在此示例中的用户分配标识创建有权从 Azure key vault 读取机密。 将此标识分配到读取机密、 生成映像，并登录到 Azure CLI 来读取图像标记的多步骤任务。

### <a name="create-a-key-vault-and-store-a-secret"></a>创建密钥保管库和存储机密

首先，如果需要创建名为的资源组*myResourceGroup*中*eastus*以下位置[az 组创建][ az-group-create]命令：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault 创建][ az-keyvault-create]命令以创建密钥保管库。 请务必指定一个唯一的密钥保管库名称。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

示例机密存储在密钥保管库中使用[az keyvault secret set] [ az-keyvault-secret-set]命令：

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

例如，你可能想要存储凭据，因此可以拉入专用映像的专用 Docker 注册表进行身份验证。

### <a name="create-an-identity"></a>创建标识

创建名为标识*myACRTasksId*中使用订阅[az 标识创建][ az-identity-create]命令。 可以使用你以前用来创建容器注册表或密钥保管库或一个不同的同一资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

若要在以下步骤中配置标识，请使用 [az identity show][az-identity-show] 命令将标识的资源 ID 和服务主体 ID 存储在变量中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>标识授予对密钥保管库读取机密

运行以下[az keyvault 设置策略][ az-keyvault-set-policy]命令以设置对密钥保管库访问策略。 以下示例允许用户分配标识从 key vault 获取机密。 更高版本需要此访问权限来成功地运行了多步骤任务。

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>标识读取器访问权限授予注册表的资源组

运行以下[创建的 az 角色分配][ az-role-assignment-create]命令，将分配标识一个读取者角色，在这种情况下为包含源注册表的资源组。 稍后需要此角色来成功地运行了多步骤任务。

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>使用用户分配的标识创建任务

现在，创建[多步骤任务](container-registry-tasks-multi-step.md)并将其分配用户分配的标识。 对于此示例任务，创建[YAML 文件](container-registry-tasks-reference-yaml.md)名为`managed-identities.yaml`本地工作目录中并粘贴以下内容。 请务必使用密钥保管库的名称替换文件中的密钥保管库名称

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

此任务将执行以下操作：

* 验证它可以访问密钥保管库中的机密。 此步骤是出于演示目的。 在实际方案中，您可能需要任务步骤以获取凭据来访问私有 Docker 中心存储库。
* 生成并将推送`mywebsite`源注册表的映像。
* 登录到 Azure CLI 将列表`my-website`图像源注册表中的标记。

创建名为的任务*msitask*并将其传递先前创建的用户分配的标识的资源 ID。 从创建此示例任务`managed-identities.yaml`因此您必须手动触发在本地工作目录中，保存的文件。

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

在命令输出中，`identity`部分显示的类型标识`UserAssigned`任务中设置。 `principalId`是标识的服务主体 ID:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>手动运行任务

使用[az acr 任务运行][ az-acr-task-run]命令以手动触发的任务。 `--set`使用参数将传递给该任务中的源注册表名称：

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

输出显示了解析机密，和映像是已成功生成并推送，则任务将记录到标识与 Azure CLI，若要从源注册表中读取图像标记：

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何使用管理的标识与 Azure 容器注册表任务和操作方法：

> [!div class="checklist"]
> * 启用系统分配标识或 ACR 任务上用户分配
> * 授予对 Azure 资源，例如其他 Azure 容器注册表的标识访问权限
> * 使用托管的标识从任务访问的资源  

* 详细了解 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/)。

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
