---
title: 将 AAD 标识用于 web 服务
titleSuffix: Azure Machine Learning
description: 将 AAD 标识用于 Azure Kubernetes 服务中的 web 服务，以便在评分期间访问云资源。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122839"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用机器学习 web 服务 Azure AD 标识

在本操作方法指南中，你将了解如何为 Azure Kubernetes 服务中已部署的机器学习模型分配 Azure Active Directory （AAD）标识。 使用[Aad Pod 标识](https://github.com/Azure/aad-pod-identity)项目，应用程序可以通过使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)和 Kubernetes 基元与 AAD 安全地访问云资源。 这使 web 服务可以安全地访问 Azure 资源，而无需在 `score.py` 脚本中直接嵌入凭据或管理令牌。 本文介绍在 Azure Kubernetes Service 群集中创建和安装 Azure 标识并将该标识分配到已部署的 web 服务的步骤。

## <a name="prerequisites"></a>先决条件

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[用于 PYTHON 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或[Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 使用 `kubectl` 命令访问 AKS 群集。 有关详细信息，请参阅[连接到群集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- 部署到 AKS 群集的 Azure 机器学习 web 服务。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>在 AKS 群集中创建和安装 Azure 标识

1. 若要确定 AKS 群集是否启用了 RBAC，请使用以下命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    如果启用了 RBAC，则此命令将返回 `true` 的值。 此值确定要在下一步中使用的命令。

1. 若要在 AKS 群集中安装[AAD Pod 标识](https://github.com/Azure/aad-pod-identity#getting-started)，请使用以下命令之一：

    * 如果你的 AKS 群集**启用了 RBAC** ，请使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * 如果 AKS 群集**未启用 RBAC**，请使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        命令的输出类似于以下文本：

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. 按照 AAD Pod 标识项目页中所示的步骤[创建一个 Azure 标识](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)。

1. 按照 AAD Pod 标识项目页中所示的步骤[安装 Azure 标识](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. 按照 AAD Pod 标识项目页中所示的步骤[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)。

1. 如果在上一步中创建的 Azure 标识与 AKS 群集不在同一资源组中，请按照 AAD Pod 标识项目页中所示的步骤，按照[设置 MIC 的权限](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic)。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>将 Azure 标识分配到机器学习 web 服务

以下步骤使用在上一部分中创建的 Azure 标识，并通过**选择器标签**将其分配给 AKS web 服务。

首先，在 AKS 群集中标识要分配 Azure 标识的部署的名称和命名空间。 可以通过运行以下命令来获取此信息。 命名空间应为 Azure 机器学习工作区名称，部署名称应为终结点名称，如门户中所示。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

通过编辑部署规范将 Azure 标识选择器标签添加到部署。选择器值应为在[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)的步骤5中定义的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

编辑部署以添加 Azure 标识选择器标签。 请参阅下面的 "`/spec/template/metadata/labels`" 部分。 应会看到值，如 `isazuremlapp: “true”`。 添加 aad 标识标签，如下所示。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

若要验证是否已正确添加标签，请运行以下命令。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

若要查看所有 pod 状态，请运行以下命令。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

盒启动并运行后，此部署的 web 服务现在可以通过 Azure 标识访问 Azure 资源，而无需在代码中嵌入凭据。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>将适当的角色分配给 Azure 标识

[将 Azure 托管标识分配到适当的角色](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)以访问其他 Azure 资源。 确保所分配的角色具有正确的**数据操作**。 例如，[存储 Blob 数据读取器角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)将具有对存储 blob 的读取权限，而一般[读者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)可能不会。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>将 Azure 标识用于机器学习 web 服务

将模型部署到 AKS 群集。 `score.py` 脚本可以包含指向 Azure 标识有权访问的 Azure 资源的操作。 确保已为尝试访问的资源安装了所需的客户端库依赖项。 下面是几个示例，介绍如何使用 Azure 标识从服务访问不同的 Azure 资源。

### <a name="access-key-vault-from-your-web-service"></a>从 web 服务访问 Key Vault

如果你已向 Azure 标识提供对**Key Vault**内机密的读取访问权限，则你的 `score.py` 可以使用以下代码访问它。

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>从 web 服务访问 Blob

如果你已向 Azure 标识授予对**存储 Blob**内数据的读取访问权限，则你的 `score.py` 可以使用以下代码访问它。

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>后续步骤

* 有关如何使用 Python Azure 标识客户端库的详细信息，请参阅 GitHub 上的[存储库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)。
* 有关将模型部署到 Azure Kubernetes Service 群集的详细指南，请参阅操作[方法](how-to-deploy-azure-kubernetes-service.md)。