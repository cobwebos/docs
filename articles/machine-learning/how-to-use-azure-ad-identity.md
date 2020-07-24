---
title: 对 Web 服务使用 AAD 标识
titleSuffix: Azure Machine Learning
description: 对 Azure Kubernetes 服务中的 Web 服务使用 AAD 标识，以便在评分期间访问云资源。
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: aa434a4e19321e88e388661ccb488f15c98d3a0f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078076"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>对 Azure Kubernetes 服务中的机器学习 Web 服务使用 Azure AD 标识

本操作指南介绍如何将 Azure Active Directory (AAD) 标识分配到 Azure Kubernetes 服务中部署的机器学习模型。 [AAD Pod 标识](https://github.com/Azure/aad-pod-identity)项目允许应用程序使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)和 Kubernetes 基元通过 AAD 安全访问云资源。 这样，Web 服务就可以安全访问你的 Azure 资源，而无需在 `score.py` 脚本中嵌入凭据或直接在其中管理令牌。 本文解释在 Azure Kubernetes 服务群集中创建和安装 Azure 标识，并将该标识分配到已部署的 Web 服务的步骤。

## <a name="prerequisites"></a>先决条件

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 使用 `kubectl` 命令访问 AKS 群集。 有关详细信息，请参阅[连接到群集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- 已部署到 AKS 群集的 Azure 机器学习 Web 服务。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>在 AKS 群集中创建并安装 Azure 标识

1. 若要确定是否为 AKS 群集启用了 RBAC，请使用以下命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    如果启用了 RBAC，此命令将返回 `true` 值。 此值确定了要在下一步骤中使用的命令。

1. 若要在 AKS 群集中安装 [AAD Pod 标识](https://github.com/Azure/aad-pod-identity#getting-started)，请使用以下命令之一：

    * 如果 AKS 群集**已启用 RBAC**，请使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * 如果 AKS 群集**未启用 RBAC**，请使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        该命令的输出类似于以下文本：

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. 按照 AAD Pod 标识项目页中所述的步骤[创建 Azure 标识](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)。

1. 按照 AAD Pod 标识项目页中所述的步骤[安装 Azure 标识](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. 按照 AAD Pod 标识项目页中所述的步骤[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)。

1. 如果在上一步骤中创建的 Azure 标识不在 AKS 群集所在的资源组中，请遵循 AAD Pod 标识项目页中所述的[设置 MIC 的权限](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic)步骤。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>将 Azure 标识分配到机器学习 Web 服务

以下步骤使用上一部分中创建的 Azure 标识，并通过**选择器标签**将其分配到 AKS Web 服务。

首先，在要将 Azure 标识分配到的 AKS 群集中，确定部署的名称和命名空间。 可运行以下命令获取此信息。 命名空间应是 Azure 机器学习工作区名称，部署名称应是门户中所示的终结点名称。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

通过编辑部署规范将 Azure 标识选择器标签添加到部署。选择器值应是在[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)的步骤 5 中定义的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

编辑部署以添加 Azure 标识选择器标签。 转到 `/spec/template/metadata/labels` 下面的以下节。 应会看到类似于 `isazuremlapp: “true”` 的值。 按如下所示添加 aad-pod-identity 标签。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

若要验证是否正确添加了该标签，请运行以下命令。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

若要查看所有 Pod 状态，请运行以下命令。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

启动并运行 Pod 后，此部署的 Web 服务现在可以通过 Azure 标识访问 Azure 资源，而无需在代码中嵌入凭据。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>将适当的角色分配到 Azure 标识

[为 Azure 托管标识分配适当的角色](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)，以访问其他 Azure 资源。 确保分配的角色具有正确的**数据操作**。 例如，[存储 Blob 数据读取者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)对存储 Blob 拥有读取权限，而普通的[读取者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)可能没有这些权限。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>对机器学习 Web 服务使用 Azure 标识

将模型部署到 AKS 群集。 `score.py` 脚本可以包含指向 Azure 标识有权访问的 Azure 资源的操作。 确保为你要尝试访问的资源安装了所需的客户端库依赖项。 下面几个示例演示如何使用 Azure 标识从服务访问不同的 Azure 资源。

### <a name="access-key-vault-from-your-web-service"></a>从 Web 服务访问 Key Vault

如果为 Azure 标识授予了对 **Key Vault** 中某个机密的读取访问权限，则 `score.py` 可以使用以下代码访问该机密。

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

> [!IMPORTANT]
> 此示例使用 DefaultAzureCredential。 若要使用特定访问策略授予标识访问权限，请参阅[第4部分：从你那里检索机密 Azure Key Vault](../key-vault/general/authentication.md#part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python)。

### <a name="access-blob-from-your-web-service"></a>从 web 服务访问 Blob

如果已向 Azure 标识授予对**存储 Blob**内的数据的读取访问权限，则 `score.py` 可以使用以下代码对其进行访问。

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
