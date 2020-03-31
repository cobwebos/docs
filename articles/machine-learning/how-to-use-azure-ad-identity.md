---
title: 将 AAD 标识与 Web 服务一起使用
titleSuffix: Azure Machine Learning
description: 在 Azure Kubernetes 服务中的 Web 服务中使用 AAD 标识，在评分期间访问云资源。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122839"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>在 Azure 库伯奈斯服务中使用 Azure AD 标识与机器学习 Web 服务

在此方法中，您将了解如何在 Azure Kubernetes 服务中为已部署的机器学习模型分配 Azure 活动目录 （AAD） 标识。 [AAD Pod 标识](https://github.com/Azure/aad-pod-identity)项目允许应用程序使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)和库伯奈斯基元安全地使用 AAD 访问云资源。 这允许 Web 服务安全地访问 Azure 资源，而无需直接在`score.py`脚本中嵌入凭据或管理令牌。 本文介绍在 Azure Kubernetes 服务群集中创建和安装 Azure 标识并将标识分配给已部署的 Web 服务的步骤。

## <a name="prerequisites"></a>先决条件

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 使用`kubectl`命令访问 AKS 群集。 有关详细信息，请参阅[连接到群集](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- 部署到 AKS 群集的 Azure 机器学习 Web 服务。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>在 AKS 群集中创建和安装 Azure 标识

1. 要确定 AKS 群集是否启用了 RBAC，请使用以下命令：

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    此命令返回`true`是否启用了 RBAC 的值。 此值确定在下一步中要使用的命令。

1. 要在 AKS 群集中安装[AAD Pod 标识](https://github.com/Azure/aad-pod-identity#getting-started)，请使用以下命令之一：

    * 如果您的 AKS 群集**启用了 RBAC，** 请使用以下命令：
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * 如果您的 AKS 群集**未启用 RBAC，** 请使用以下命令：
    
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

1. [按照](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)AAD Pod 标识项目页中显示的步骤创建 Azure 标识。

1. 按照 AAD Pod 标识项目页中显示的步骤[安装 Azure 标识](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. 按照 AAD Pod 标识项目页中显示的步骤[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)。

1. 如果上一步中创建的 Azure 标识与 AKS 群集不在同一资源组中，请按照 AAD Pod 标识项目页中显示的步骤操作["为 MIC 设置权限](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic)"。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>将 Azure 标识分配给机器学习 Web 服务

以下步骤使用上一节中创建的 Azure 标识，并通过**选择器标签**将其分配给 AKS Web 服务。

首先，在要分配 Azure 标识的 AKS 群集中标识部署的名称和命名空间。 您可以通过运行以下命令来获取此信息。 命名空间应为 Azure 机器学习工作区名称，并且部署名称应为终结点名称，如门户所示。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

通过编辑部署规范，将 Azure 标识选择器标签添加到部署中。选择器值应该是在[安装 Azure 标识绑定](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)的步骤 5 中定义的值。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

编辑部署以添加 Azure 标识选择器标签。 转到 下一节。 `/spec/template/metadata/labels` 您应该看到值，如`isazuremlapp: “true”`。 添加 aad-pod 标识标签，如下所示。

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

要验证标签是否正确添加，请运行以下命令。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

要查看所有 Pod 状态，运行以下命令。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

启动并运行窗格后，此部署的 Web 服务现在将能够通过 Azure 标识访问 Azure 资源，而无需将凭据嵌入到代码中。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>将适当的角色分配给 Azure 标识

[使用适当的角色分配 Azure 托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)以访问其他 Azure 资源。 确保您分配的角色具有正确的**数据操作**。 例如，存储[Blob 数据读取器角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)将具有对存储 Blob 的读取权限，而通用[读取器角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)可能没有。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>将 Azure 标识与机器学习 Web 服务一起使用

将模型部署到 AKS 群集。 该`score.py`脚本可以包含指向 Azure 标识有权访问的 Azure 资源的操作。 确保已为尝试访问的资源安装了所需的客户端库依赖项。 下面是如何使用 Azure 标识从服务访问不同的 Azure 资源的几个示例。

### <a name="access-key-vault-from-your-web-service"></a>从 Web 服务访问密钥保管库

如果已授予 Azure 标识对**密钥保管库**内机密的读取访问权限，则可以使用`score.py`以下代码访问它。

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

### <a name="access-blob-from-your-web-service"></a>从 Web 服务访问 Blob

如果已授予 Azure 标识对**存储 Blob**中数据的读取访问权限，`score.py`则可以使用以下代码访问它。

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
* 有关将模型部署到 Azure 库伯奈斯服务群集的详细信息，请参阅[方法](how-to-deploy-azure-kubernetes-service.md)。