---
title: 将托管标识用于 Azure 容器注册表任务
description: 通过为 Azure 资源分配托管标识, 提供 azure 容器注册表任务对 Azure 资源的访问权限, 包括其他专用容器注册表。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311538"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 任务中使用 Azure 托管标识 

使用[azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)对 azure 容器注册表或其他 azure 资源的 ACR 任务进行身份验证, 而无需在代码中提供或管理凭据。 例如, 使用托管标识将容器映像提取或推送到其他注册表, 作为任务中的一个步骤。

在本文中, 你将了解有关托管标识的详细信息以及如何:

> [!div class="checklist"]
> * 在 ACR 任务上启用系统分配的标识或用户分配的标识
> * 向标识授予对 Azure 资源 (例如其他 Azure 容器注册表) 的访问权限
> * 使用托管标识从任务访问资源 

若要创建 Azure 资源, 本文要求运行 Azure CLI 版本2.0.66 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识可在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管标识。 你可以使用托管标识配置[某些 Azure 资源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), 包括 ACR 任务。 然后使用该标识访问其他 Azure 资源，而无需在代码或脚本中传递凭据。

托管标识有两种类型：

* *用户分配的标识*，可以将其分配给多个资源，并根据需要持久保存。 用户分配的标识现提供预览版。

* *系统管理的标识*, 它对特定资源 (如 ACR 任务) 是唯一的, 并在该资源的生存期内持续。

使用托管标识设置 Azure 资源后, 可以将该标识授予对其他资源的访问权限, 就像任何安全主体一样。 例如, 为 Azure 中的专用容器注册表分配包含请求、推送和拉取或其他权限的托管标识。 （有关完整的注册表角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。）可以授予标识对一个或多个资源的访问权限。

## <a name="create-container-registries"></a>创建容器注册表

对于本教程, 需要三个容器注册表:

* 使用第一个注册表来创建和执行 ACR 任务。 本文中, 此源注册表名为*myregistry*。 
* 第二个和第三个注册表是用于推送其生成的映像的第一个示例任务的目标注册表。 在本文中, 目标注册表名称为*customregistry1*和*customregistry2*。

在后面的步骤中将替换为你自己的注册表名称。

如果还没有所需的 Azure 容器注册表, 请参阅[快速入门:使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 你还不需要将映像推送到注册表。

## <a name="example-task-with-a-system-assigned-identity"></a>例如：使用系统分配的标识的任务

此示例演示如何使用系统分配的标识创建[多步骤任务](container-registry-tasks-multi-step.md)。 该任务生成一个映像, 然后使用该标识通过两个目标注册表进行身份验证, 以便推送映像。

此示例任务的步骤在名为`testtask.yaml`的[YAML 文件](container-registry-tasks-reference-yaml.md)中定义。 该文件位于 " [acr-任务](https://github.com/Azure-Samples/acr-tasks)示例存储库" 的 multipleRegistries 目录中。 此文件将在此处重现:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>用系统分配的标识创建任务

通过执行以下[az acr task create][az-acr-task-create]命令, 创建*多注册表*任务。 任务上下文是示例存储库的 multipleRegistries 文件夹, 命令引用存储库中的文件`testtask.yaml` 。 没有其他值的参数会为任务创建系统分配的标识。`--assign-identity` 此任务已设置, 因此你必须手动对其进行触发, 但你可以将其设置为在提交到存储库时运行, 或进行拉取请求。 

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

在命令输出中, `identity`部分显示在任务中设置类型`SystemAssigned`的标识。 `principalId`是标识的服务主体 ID:

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

使用[az acr task show][az-acr-task-show]命令将存储`principalId`在变量中, 以便在后面的命令中使用:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>向两个目标容器注册表授予标识推送权限

在本部分中, 为系统分配的标识授予推送到名为 " *customregistry1* " 和 " *customregistry2*" 的两个目标注册表项的权限。

首先, 使用[az acr show][az-acr-show]命令获取每个注册表的资源 ID 并将 id 存储在变量中:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

使用[az role assign create][az-role-assignment-create]命令将`acrpush`角色分配给每个注册表。 此角色有权请求和将映像推送到容器注册表。

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>将目标注册表凭据添加到任务

现在, 使用[az acr task credential add][az-acr-task-credential-add]命令将标识凭据添加到任务, 以便它能够在两个目标注册表中进行身份验证。

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

使用[az acr task run][az-acr-task-run]命令手动触发任务。 参数用于将两个目标注册表项的登录服务器名称作为任务变量`REGISTRY1`和`REGISTRY2`的值传入。 `--set`

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

## <a name="example-task-with-a-user-assigned-identity"></a>例如：具有用户分配的标识的任务

在此示例中, 将创建一个用户分配的标识, 其中包含从 Azure 密钥保管库中读取机密的权限。 将此标识分配给读取机密的多步骤任务, 生成映像, 然后登录 Azure CLI 以读取图像标记。

### <a name="create-a-key-vault-and-store-a-secret"></a>创建密钥保管库并存储机密

首先, 如果需要, 请在*eastus*位置创建一个名为*myResourceGroup*的资源组, 并在其中包含以下[az group create][az-group-create]命令:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault create][az-keyvault-create]命令创建密钥保管库。 请确保指定唯一的密钥保管库名称。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用[az keyvault secret set][az-keyvault-secret-set]命令将示例机密存储在密钥保管库中:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

例如, 你可能想要存储凭据以使用专用 Docker 注册表进行身份验证, 以便可以提取专用映像。

### <a name="create-an-identity"></a>创建标识

在订阅中使用[az identity create][az-identity-create]命令创建名为*myACRTasksId*的标识。 你可以使用之前使用的同一资源组创建容器注册表或密钥保管库, 或使用其他资源组。

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

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>向 keyvault 授予标识访问权限以读取机密

运行以下[az keyvault set-policy][az-keyvault-set-policy]命令来设置密钥保管库上的访问策略。 以下示例允许用户分配的标识从密钥保管库获取机密。 稍后需要此访问权限才能成功运行多步骤任务。

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>向标识读取器授予对注册表的资源组的访问权限

运行以下[az role assign create][az-role-assignment-create]命令, 将标识指定为读取者角色, 在本例中为包含源注册表的资源组。 稍后需要此角色来成功运行多步骤任务。

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>创建具有用户分配的标识的任务

现在创建一个[多步骤任务](container-registry-tasks-multi-step.md), 并为其分配用户分配的标识。 对于此示例任务, 请在本地工作目录`managed-identities.yaml`中创建一个名为的[YAML 文件](container-registry-tasks-reference-yaml.md)并粘贴以下内容。 请确保将该文件中的密钥保管库名称替换为你的密钥保管库的名称

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

此任务将执行以下操作:

* 验证它是否可以访问密钥保管库中的机密。 此步骤用于演示目的。 在实际方案中, 你可能需要使用任务步骤来获取访问专用 Docker 中心存储库所需的凭据。
* 生成`mywebsite`映像并将其推送到源注册表。
* 登录到 Azure CLI 以列出源注册表`my-website`中的图像标记。

创建名为*msitask*的任务, 并向其传递先前创建的用户分配的标识的资源 ID。 此示例任务是从你保存`managed-identities.yaml`在本地工作目录中的文件创建的, 因此你必须手动触发它。

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

在命令输出中, `identity`部分显示在任务中设置类型`UserAssigned`的标识。 `principalId`是标识的服务主体 ID:

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

使用[az acr task run][az-acr-task-run]命令手动触发任务。 `--set`参数用于将源注册表名称传入任务:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

输出显示密码已解析, 映像已成功生成并推送, 并且该任务登录到带有标识的 Azure CLI, 以便从源注册表读取映像标记:

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

本文介绍了如何在 Azure 容器注册表任务中使用托管标识, 以及如何:

> [!div class="checklist"]
> * 在 ACR 任务上启用系统分配的标识或用户分配的标识
> * 向标识授予对 Azure 资源 (例如其他 Azure 容器注册表) 的访问权限
> * 使用托管标识从任务访问资源  

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
