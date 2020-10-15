---
title: 使用客户管理的密钥加密 Azure Kubernetes Service (AKS) 中的 Azure 磁盘
description: 自带密钥 (BYOK) 来加密 AKS OS 和数据磁盘。
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 8d0f66d034bb0566674e521ced3e3887e29e3aaa
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071721"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>对 Azure Kubernetes Service (AKS) 中的 Azure 磁盘使用自带密钥 (BYOK)

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了更进一步控制加密密钥，可以提供客户托管密钥，将其用于对 AKS 群集的 OS 和数据磁盘进行静态加密。 详细了解 [Linux][customer-managed-keys-linux] 和 [Windows][customer-managed-keys-windows] 上的客户管理密钥。

## <a name="limitations"></a>限制
* 数据磁盘加密支持仅限于运行 Kubernetes 1.17 及更高版本的 AKS 群集。
* 只有在创建 AKS 群集时才能启用使用客户管理的密钥对 OS 和数据磁盘进行加密。

## <a name="prerequisites"></a>先决条件
* 使用密钥保管库加密托管磁盘时，必须为 *Azure 密钥保管库*启用软删除和清除保护。
* 需要 Azure CLI 版本 2.11.1 或更高版本。

## <a name="create-an-azure-key-vault-instance"></a>创建 Azure Key Vault 实例

使用 Azure Key Vault 实例来存储密钥。  可以通过 Azure 门户[使用 Azure Key Vault 配置客户管理的密钥][byok-azure-portal]

创建一个新的*资源组*，然后创建一个新的*密钥保管库*实例，并启用软删除和清除保护。  确保对每个命令使用同一区域和资源组名称。

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>创建 DiskEncryptionSet 的实例

将 *myKeyVaultName* 替换为你的密钥保管库的名称。  若要完成以下步骤，还需一个存储在 Azure Key Vault 中的“密钥”。  可以将现有密钥存储在通过前面的步骤创建的 Key Vault 中，也可以[生成新密钥][key-vault-generate]并将下面的 *myKeyName* 替换为你的密钥的名称。
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>向 DiskEncryptionSet 授予对密钥保管库的访问权限

使用在前面的步骤中创建的 DiskEncryptionSet 和资源组，并授予 DiskEncryptionSet 资源对 Azure 密钥保管库的访问权限。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>创建一个新的 AKS 群集，并对 OS 磁盘加密

创建一个**新的资源组**和 AKS 群集，然后使用密钥对 OS 磁盘进行加密。 只有高于 1.17 的 Kubernetes 版本才支持客户托管密钥。 

> [!IMPORTANT]
> 确保为 AKS 群集创建新的资源组

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

向上面创建的群集添加新的节点池时，会使用在创建过程中提供的客户托管密钥对 OS 磁盘进行加密。

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>加密 AKS 群集数据磁盘（可选）
如果 v1.17.2 没有为数据磁盘提供密钥，则使用 OS 磁盘加密密钥对数据磁盘进行加密，并且还可以使用其他密钥对 AKS 数据磁盘进行加密。

> [!IMPORTANT]
> 确保你具有正确的 AKS 凭据。 对于在其中部署了 diskencryptionset 的资源组，服务主体需要有参与者访问权限。 否则，你将收到一条错误消息，指出服务主体没有权限。

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

创建一个名为 **byok-azure-disk.yaml** 的文件，在其中包含以下信息。  将 myAzureSubscriptionId、myResourceGroup 和 myDiskEncrptionSetName 替换为你的值并应用 yaml。  请确保使用在其中部署了 DiskEncryptionSet 的资源组。  如果使用 Azure Cloud Shell，则可使用 vi 或 nano 来创建此文件，就像在虚拟或物理系统上工作一样：

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
接下来，在 AKS 群集中运行此部署：
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>后续步骤

查看 [AKS 群集安全性最佳做法][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/windows/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/linux/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/windows/disk-encryption.md#supported-regions