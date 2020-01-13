---
title: 使用客户托管的密钥加密 Azure Kubernetes Service （AKS）中的 Azure 磁盘
description: 自带密钥（BYOK）加密 AKS OS 和数据磁盘。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 96e7c401578ca8311bfe0e6b5477a9d8cab1a24e
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912729"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>将自己的密钥（BYOK）与 azure Kubernetes 服务（AKS）中的 Azure 磁盘结合在一起

Azure 存储加密静态存储帐户中的所有数据。 默认情况下，使用 Microsoft 托管的密钥对数据进行加密。 为了进一步控制加密密钥，你可以提供[客户管理的密钥][customer-managed-keys]，用于对 AKS 群集的 OS 和数据磁盘进行加密。

> [!NOTE]
> 支持 Linux 和基于 Windows 的 AKS 群集。

## <a name="before-you-begin"></a>开始之前

* 本文假设你要创建*新的 AKS 群集*。  还需要使用或创建用于存储加密密钥的 Azure Key Vault 的实例。

* 使用 Key Vault 对托管磁盘进行加密时，必须为*Azure Key Vault*启用软删除和清除保护。

* 需要 Azure CLI 版本2.0.79 或更高版本，以及 aks-preview 0.4.26 扩展

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此，这些功能并不用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>安装最新的 AKS CLI 预览扩展

若要使用客户托管的密钥，需要*aks* CLI 扩展版本0.4.26 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>创建 Azure Key Vault 实例以存储密钥

你可以选择使用 Azure 门户来[配置客户管理的密钥，Azure Key Vault][byok-azure-portal]

创建新的*资源组*，然后创建新的*Key Vault*实例，并启用软删除和清除保护。

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>创建 DiskEncryptionSet 的实例

需要一个存储在 Azure Key Vault 中的*密钥*才能完成以下步骤。  请在创建的 Key Vault 中存储现有密钥，或[生成密钥][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>向 DiskEncryptionSet 资源授予对密钥保管库的访问权限

使用在前面的步骤中创建的 DiskEncryptionSet 和资源组，并授予对 Azure Key Vault 的 DiskEncryptionSet 资源访问权限。

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>创建新的 AKS 群集，并使用托管密钥对 OS 磁盘进行加密

创建新的资源组和 AKS 群集，并使用密钥对 OS 磁盘进行加密。 只有1.17 版的 kubernetes 支持客户托管密钥

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n diskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0
```

向上面创建的群集添加新节点池时，在创建过程中提供的客户托管密钥用于对 OS 磁盘进行加密

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>使用客户管理的密钥加密 AKS 群集数据磁盘

还可以用自己的密钥加密 AKS 数据磁盘。  将 myResourceGroup 和 myDiskEncryptionSetName 替换为实际值，并应用 yaml。

确保具有正确的 AKS 凭据。 服务主体将需要具有对 diskencryptionset 所在的资源组的参与者访问权限。 否则，你将收到一条错误消息，建议服务主体没有权限。

创建一个名为**byok**的文件，其中包含以下信息。  将 myResourceGroup 和 myDiskEncrptionSetName 替换为你的值。

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
接下来，在 AKS 群集中运行此部署：
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>限制

* Kubernetes 版本1.17 及更高版本支持 OS 磁盘加密   
* 仅适用于支持 BYOK 的区域
* 这仅适用于新的 AKS 群集，无法升级现有群集
* 使用虚拟机规模集的 AKS 群集是必需的，不支持虚拟机可用性集


## <a name="next-steps"></a>后续步骤

查看[AKS 群集安全性的最佳实践][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
