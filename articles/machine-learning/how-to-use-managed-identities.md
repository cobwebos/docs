---
title: '将托管标识用于访问控制 (预览) '
titleSuffix: Azure Machine Learning
description: 了解如何使用托管标识从 Azure 机器学习工作区控制对 Azure 资源的访问。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6bcc4ac5561a8bdb721018aa05bf2376579b627b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079645"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>将托管标识与 Azure 机器学习 (预览版一起使用) 

利用[托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，你可以使用*访问资源所需的最低权限*配置工作区。 

以可信方式配置 Azure 机器学习工作区时，务必确保与工作区关联的不同服务具有正确的访问级别。 例如，在机器学习工作流期间，工作区需要访问 Azure 容器注册表 (适用于 Docker 映像的 ACR) ，以及用于定型数据的存储帐户。 

此外，托管标识允许对权限进行精细控制，例如，可以授予或撤消从特定的计算资源到特定 ACR 的访问权限。

本文介绍如何使用托管标识执行以下操作：

 * 为 Azure 机器学习工作区配置和使用 ACR，无需让管理员用户访问 ACR。
 * 访问工作区外部的私有 ACR，以获取用于定型或推理的基本图像。
 * 使用托管标识（而不是存储访问密钥）访问用于定型的数据集。

> [!IMPORTANT]
> 使用托管标识控制对资源的访问，Azure 机器学习当前为预览版。 预览功能按 "原样" 提供，不保证支持或服务级别协议。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)
- [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
- 若要分配角色，你的 Azure 订阅的登录名必须具有 [托管标识运算符](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 角色或授予所需操作的其他角色 (如 __所有者__) 。
- 必须熟悉如何创建和使用 [托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="configure-managed-identities"></a>配置托管标识

在某些情况下，必须禁止管理员用户对 Azure 容器注册表的访问权限。 例如，可以共享 ACR，你需要禁止其他用户进行管理访问。 或者，订阅级别策略不允许创建启用了管理员用户的 ACR。

> [!IMPORTANT]
> 使用 Azure 机器学习对 Azure 容器实例上的推理 (ACI) ， __需要__在 ACR 上管理用户访问权限。 如果计划将模型部署到 ACI 进行推理，请不要禁用此功能。

在不启用管理员用户访问权限的情况下创建 ACR 时，将使用托管标识来访问 ACR 以生成和请求 Docker 映像。

创建工作区时，可以将自己的 ACR 与管理员用户禁用。 或者，让 Azure 机器学习创建工作区 ACR，以后再禁用管理员用户。

### <a name="bring-your-own-acr"></a>自带 ACR

如果订阅策略不允许使用 ACR 管理员用户，则应首先创建无管理员用户的 ACR，然后将其与工作区关联。 此外，如果你有已禁用管理员用户的现有 ACR，你可以将其附加到工作区。

[从 Azure CLI 创建 ACR](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) ，无需设置 ```--admin-enabled``` 参数，也不允许管理员用户 Azure 门户。 然后，在创建 Azure 机器学习工作区时，指定 ACR 的 Azure 资源 ID。 下面的示例演示如何创建使用现有 ACR 的新 Azure ML 工作区：

> [!TIP]
> 若要获取参数的值 `--container-registry` ，请使用 [az acr show](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) 命令显示 acr 的信息。 `id`字段包含 ACR 的资源 ID。

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>让 Azure 机器学习服务创建工作区 ACR

如果没有引入自己的 ACR，Azure 机器学习服务在执行需要的操作时，将为您创建一个。 例如，将训练运行提交到机器学习计算、构建环境或部署 web 服务终结点。 工作区创建的 ACR 将启用管理员用户，您需要手动禁用管理员用户。

1. 创建新的工作区

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. 执行需要 ACR 的操作。 例如，有关 [定型模型的教程](tutorial-train-models-with-aml.md)。

1. 获取由群集创建的 ACR 名称：

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    此命令返回类似于以下文本的值。 只需要文本的最后一部分，即 ACR 实例名称：

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. 更新 ACR 以禁用管理员用户：

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>使用托管标识创建计算以访问 Docker 映像以进行培训

若要访问工作区 ACR，请创建启用了系统分配的托管标识的机器学习计算群集。 创建计算时或 Azure CLI 使用，可以从 Azure 门户或工作室启用标识

# <a name="python"></a>[Python](#tab/python)

使用 [AmlComputeProvisioningConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)创建计算群集时，请使用 `identity_type` 参数设置托管标识类型。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要了解在工作室中创建计算群集时配置托管标识，请参阅 [设置托管标识](how-to-create-attach-compute-studio.md#managed-identity)。

---

托管标识在工作区 ACR 上自动授予 ACRPull 角色，以允许拉取 Docker 映像以进行定型。

> [!NOTE]
> 如果首先创建计算，则在创建工作区 ACR 之前，必须手动分配 ACRPull 角色。

## <a name="access-base-images-from-private-acr"></a>从私有 ACR 访问基本映像

默认情况下，Azure 机器学习使用来自 Microsoft 托管的公共存储库的 Docker 基本映像。 然后，在这些映像上构建定型或推理环境。 有关详细信息，请参阅 [什么是 ML 环境？](concept-environments.md)。

若要在企业内部使用自定义基本映像，可以使用托管标识访问私有 ACR。 下面是两个用例：

 * 按原样使用基准映像进行定型。
 * 使用自定义映像作为基础构建 Azure 机器学习托管映像。

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>按原样将 Docker 基本映像请求到机器学习计算群集

如前面所述，按已启用系统分配的托管标识创建机器学习计算群集。 然后，确定托管标识的主体 ID。

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

或者，你可以更新计算群集来分配用户分配的托管标识：

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

若要允许计算群集拉取基本映像，请在私有 ACR 上授予托管服务标识 ACRPull 角色

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

最后，在提交训练运行时，在 [环境定义](how-to-use-environments.md#use-existing-environments)中指定基本映像位置。

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> 若要确保基本映像直接拉取到计算资源，请设置 `user_managed_dependencies = True` ，并不要指定 Dockerfile。 否则 Azure 机器学习服务将尝试生成新的 Docker 映像并失败，因为只有计算群集才能从 ACR 中提取基本映像。

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>从私有 ACR 构建 Azure 机器学习托管环境到基本映像以进行定型或推理

在此方案中，Azure 机器学习服务在从私有 ACR 提供的基本映像的顶层建立定型或推理环境。 由于图像生成任务在工作区 ACR 上使用 ACR 任务进行，因此您必须执行其他步骤以允许访问。

1. 创建 __用户分配的托管标识__ ，并授予标识 ACRPull 访问 __私有 ACR__的权限。  
1. 向工作区 __系统分配的托管__ 标识授予对上一步中 __用户分配的托管标识__ 的托管标识操作员角色。 此角色允许工作区将用户分配的托管标识分配给 ACR 任务用于构建托管环境。 

    1. 获取工作区系统分配的托管标识的主体 ID：

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. 授予托管标识操作员角色：

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI 资源 ID 是用户分配的标识的 Azure 资源 ID （格式为） `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` 。

1. 使用[Workspace.set_connection 方法](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)在工作区连接中指定__用户分配的托管标识__的外部 ACR 和客户端 ID：

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

完成配置后，可以在生成定型或推理的环境时使用私有 ACR 中的基本映像。 下面的代码段演示如何在环境定义中指定基本映像 ACR 和映像名称：

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

或者，您可以使用 [RegistryIdentity](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py)在环境定义本身中指定托管标识资源 URL 和客户端 ID。 如果显式使用注册表标识，则它会覆盖前面指定的任何工作区连接：

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="access-training-data"></a>访问定型数据

如前面所述，使用托管标识创建了机器学习计算群集后，可以使用该标识访问无存储帐户密钥的定型数据。 对于此方案，可以使用系统分配的托管标识。

### <a name="grant-compute-managed-identity-access-to-storage-account"></a>向计算托管标识授予对存储帐户的访问权限

[向托管标识授予](https://docs.microsoft.com/azure/storage/common/storage-auth-aad#assign-azure-roles-for-access-rights) 存储定型数据的存储帐户的 "读取者" 角色。

### <a name="register-data-store-with-workspace"></a>向工作区注册数据存储

分配托管标识后，可以创建数据存储，而无需指定存储凭据。

```python
from azureml.core import Datastore

blob_dstore = Datastore.register_azure_blob_container(workspace=workspace,
                                                      datastore_name='my-datastore',
                                                      container_name='my-container',
                                                      account_name='my-storage-account')
```

### <a name="submit-training-run"></a>提交定型运行任务

使用数据存储提交定型运行时，机器学习计算将使用其托管标识来访问数据。

## <a name="use-docker-images-for-inference"></a>使用 Docker 映像进行推理

如前面所述，在没有管理员用户的情况下配置 ACR 后，可以使用 Azure Kubernetes 服务中没有管理密钥的用于推理的 Docker 映像 (AKS) 。 创建 AKS 并将其附加到工作区时，会自动为该群集的服务主体分配 ACRPull 访问工作区 ACR。

> [!NOTE]
> 如果你自带 AKS 群集，则群集必须已启用服务主体而不是托管标识。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 机器学习中的企业安全性](concept-enterprise-security.md)。
