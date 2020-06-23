---
title: 将 Azure Key Vault 与 Kubernetes 集成
description: 在本教程中，你将使用机密存储 CSI（容器存储接口）驱动程序从 Azure Key Vault 访问和检索机密，然后将其装载到 Kubernetes Pod。
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636930"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>教程：为 Kubernetes 上的机密存储 CSI 驱动程序配置并运行 Azure Key Vault 提供程序

在本教程中，你将使用机密存储 CSI（容器存储接口）驱动程序从 Azure Key Vault 访问和检索机密，然后将其装载到 Kubernetes Pod。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建服务主体
> * 部署 Azure Kubernetes 服务群集
> * 安装 Helm 和机密存储 CSI 驱动程序
> * 创建 Azure Key Vault 并设置机密
> * 创建自己的 SecretProviderClass 对象
> * 通过 Key Vault 中已装载的机密部署 Pod

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在开始学习本教程之前，请安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

## <a name="create-a-service-principal-or-use-managed-identities"></a>创建服务主体或使用托管标识

如果计划使用托管标识，可转到下一部分。

创建一个服务主体来控制可从 Azure Key Vault 访问哪些资源。 此服务主体的访问受分配给它的角色限制。 此功能使你能够控制服务主体如何管理机密。 在下面的示例中，服务主体的名称是“contosoServicePrincipal”。

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
此操作会返回一系列键/值对：

![映像](../media/kubernetes-key-vault-1.png)

复制 appID 和密码 。 稍后将需要这些凭据。



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>使用 Azure CLI 部署 Azure Kubernetes 服务群集

无需使用 Azure Cloud Shell，安装了 Azure CLI 的命令提示符（终端）就已足够。 

按照本[指南](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)操作并完成以下部分：“创建资源组”、“创建 AKS 群集”，以及“连接到群集”  。

**注意：** 如果计划使用 Pod 标识而不是服务主体， 请确保在创建 Kubernetes 群集时启用它，如下所示：

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [将 PATH 环境变量设置](https://www.java.com/en/download/help/path.xml)为已下载的“kubectl.exe”文件。
1. 使用以下命令检查 Kubernetes 版本。 此命令将输出客户端和服务器版本。 客户端版本是已安装的“kubectl.exe”，而服务器版本是正在运行群集的 Azure Kubernetes 服务。
    ```azurecli
    kubectl version
    ```
1. 确保 Kubernetes 版本是 v1.16.0 或更高版本。 此命令将同时升级 Kubernetes 群集和节点池。 可能几分钟后才会执行。 在本例中，资源组是 contosoResourceGroup，Kubernetes 群集是 contosoAKSCluster 。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 显示使用以下命令创建的 AKS 群集的元数据。 复制 principalId、clientId、subscriptionId 和 nodeResourceGroup   。
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    这是两个参数都突出显示的输出。
    
    ![插图](../media/kubernetes-key-vault-5.png) ![插图](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>安装 Helm 和机密存储 CSI 驱动程序

需要安装 [Helm](https://helm.sh/docs/intro/install/) 才能安装机密存储 CSI 驱动程序。

通过[机密存储 CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) 驱动程序接口，可获取 Azure Key Vault 实例中存储的机密内容，并使用驱动程序接口将这些机密内容装载到 Kubernetes Pod 中。

1. 检查 Helm 版本并确保其为 v3 或更高版本：
    ```azurecli
    helm version
    ```
1. 为驱动程序安装机密存储 CSI 驱动程序和 Azure Key Vault 提供程序：
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>创建 Azure Key Vault 并设置机密

按照本[指南](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)自行创建 Key Vault 并设置机密。

**注意：** 无需使用 Azure Cloud Shell，也无需创建新的资源组。 可使用之前为 Kubernetes 群集创建的资源组。

## <a name="create-your-own-secretproviderclass-object"></a>创建自己的 SecretProviderClass 对象

使用所提供的此[模板](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml)自行创建自定义 SecretProviderClass 对象，为机密存储 CSI 驱动程序提供提供程序特定的参数。 此对象将提供对 Key Vault 的标识访问。

使用提供的示例 SecretProviderClass YAML 文件。 需填写缺少的参数。 下列参数必填：

1.  **userAssignedIdentityID：** 服务主体的客户端 ID
1.  **keyvaultName：** Key Vault 的名称
1.  **对象：** 此对象将包含要装载的所有机密内容
    1.  **objectName：** 机密内容的名称
    1.  **objectType：** 对象类型（机密、密钥、证书）
1.  **resourceGroup：** 资源组的名称
1.  **subscriptionID：** Key Vault 的订阅 ID
1.  **tenantID：** Key Vault 的租户 ID（即目录 ID）

下面是更新后的模板，请以 .yaml 文件形式下载它并填写相应的必需字段。 在本例中，Key Vault 是 contosoKeyVault5，它有两个机密 - secret1 和 secret2  。

**注意：** 如果使用托管标识，则字段“usePodIdentity”必须为 true，且将字段“userAssignedIdentityID”保留为只有引号 ""   。 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
下面是“az keyvault show --name contosoKeyVault5”的控制台输出，其中包含突出显示的相关元数据：

![映像](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>分配服务主体或使用托管标识

### <a name="using-service-principal"></a>使用服务主体

如果使用服务主体， 则需要向服务主体授予访问 Key Vault 和检索机密的权限。 请完成以下步骤，来分配“读取者”角色并向服务主体授予从 Key Vault 获取机密的权限 ：

1. 将服务主体分配到现有 Key Vault。 $AZURE_CLIENT_ID 参数是在创建服务主体后复制的 appId 。
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    下面是命令的输出： 

    ![映像](../media/kubernetes-key-vault-3.png)

1. 向服务主体授予获取机密的权限：
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. 现在，你已将服务主体配置为有权从 Key Vault 读取机密。 服务主体的密码是 $AZURE_CLIENT_SECRET 。 将服务主体凭据添加为机密存储 CSI 驱动程序可访问的 Kubernetes 机密：
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**注意：** 如果稍后在部署 Kubernetes Pod 时收到错误显示客户端机密 ID 无效， 则你的客户端机密 ID 可能已过期或重置。 要解决此问题，请删除机密“secrets-store-creds”，并使用当前客户端机密 ID 创建一个新机密。 使用以下命令可删除“secrets-store-creds”：
```azurecli
kubectl delete secrets secrets-store-creds
```

如果忘记了服务主体的客户端机密 ID，可使用以下命令重置它：

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>使用托管标识

如果使用托管标识，请将特定角色分配给所创建的 AKS 群集。 
1. 若要创建/列出/读取用户分配的托管标识，需要为 AKS 群集分配[托管标识参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色。 请确保 $clientId 属于 Kubernetes 群集。

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. 将 Azure Active Directory (Azure AD) 标识安装到 AKS。
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. 创建 Azure AD 标识。 复制 clientId 和 principalId 。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. 将“读取者”角色分配给刚才为 Key Vault 创建的 Azure AD 标识。 然后向标识授予从 Key Vault 获取机密的权限。 将使用刚才创建的 Azure 标识中的 clientId 和 principalId 。
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>通过 Key Vault 中已装载的机密部署 Pod

以下命令将配置 SecretProviderClass 对象：
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>使用服务主体

如果使用服务主体， 以下命令将使用所配置的 SecretProviderClass 和 secrets-store-creds 部署 Kubernetes Pod。 这是 [linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) 和 [windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) 部署的模板。
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>使用托管标识

如果使用托管标识， 需在群集中创建 AzureIdentity，它将引用先前创建的标识。 然后，创建 AzureIdentityBinding，它将引用所创建的 AzureIdentity 。 使用下面的模板填写相应的参数，并将其另存为 podIdentityAndBinding.yaml。  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
运行以下命令来执行绑定：

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

接下来是实际部署 Pod。 下面是部署 YAML 文件，它将使用上一步中的 pod 标识绑定。 将此文件保存为 podBindingDeployment. yaml。

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

运行以下命令来部署 Pod：

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>检查状态和机密内容 
若要显示已部署的 Pod：
```azurecli
kubectl get pods
```

若要检查 Pod 的状态，请使用以下命令：
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![映像](../media/kubernetes-key-vault-4.png)

部署的 Pod 应处于“正在运行”状态。 在底部的“事件”部分中，左侧的所有事件类型都归类为“正常”
验证确定 Pod 正在运行后，可验证 Pod 是否包含来自 Key Vault 的机密。

若要显示 Pod 包含的所有机密，请执行以下操作：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

若要从特定机密中获取内容：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

验证显示的机密内容。

## <a name="next-steps"></a>后续步骤

确保 Key Vault 可恢复：
> [!div class="nextstepaction"]
> [启用软删除](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
