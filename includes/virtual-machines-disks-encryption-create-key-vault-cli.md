---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2c1f61dbb060f9df0b88f301008e3fd45a91fe8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609365"
---
1. 确保已安装了最新的 [Azure CLI](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

1. 创建 Azure Key Vault 和加密密钥的实例。

    创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为 90 天）内保留已删除的密钥。 清除保护可确保在保留期结束之前，无法永久删除已删除的密钥。 这些设置可防止由于意外删除而丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。

    > [!IMPORTANT]
    > 不要对区域使用混合大小写，如果这样做，则在将其他磁盘分配到 Azure 门户中的资源时可能会遇到问题。

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    创建一个 DiskEncryptionSet 实例。 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    授予对密钥保管库的 DiskEncryptionSet 资源访问权限。 

        > [!NOTE]
        > Azure 可能需要几分钟时间才能在 Azure Active Directory 中创建 DiskEncryptionSet 的标识。 如果在运行以下命令时收到类似于“找不到 Active Directory 对象”的错误，请等待几分钟，然后重试。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```
