---
title: 启用静态加密 Azure CLI 托管磁盘
description: 使用 Azure CLI 为托管磁盘数据启用静态加密。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 76e5ab49e354faac085be533fed1ff16c0315a5e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816824"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>使用 Azure CLI 为托管磁盘启用静态加密

Azure 磁盘存储支持将静态加密用于托管磁盘。 有关静态加密以及其他托管磁盘加密类型的概念信息，请参阅磁盘加密一文中的 " [静态加密](disk-encryption.md#double-encryption-at-rest) " 部分。

## <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>先决条件

安装最新 [Azure CLI](/cli/azure/install-az-cli2) ，并使用 [az Login](/cli/azure/reference-index)登录到 Azure 帐户。

## <a name="getting-started"></a>入门

1. 创建 Azure Key Vault 和加密密钥的实例。

    创建 Key Vault 实例时，必须启用软删除和清除保护。 软删除可确保 Key Vault 在给定的保留期（默认为 90 天）内保留已删除的密钥。 清除保护可确保在保留期结束之前，无法永久删除已删除的密钥。 这些设置可防止由于意外删除而丢失数据。 使用 Key Vault 加密托管磁盘时，这些设置是必需的。

    
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

1.    创建 DiskEncryptionSet，并将 encryptionType 设置为 EncryptionAtRestWithPlatformAndCustomerKeys。 使用 Azure 资源管理器 (ARM) 模板中的 API 版本 **2020-05-01** 。 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    授予对密钥保管库的 DiskEncryptionSet 资源访问权限。 

        > [!NOTE]
        > Azure 可能需要几分钟时间才能在 Azure Active Directory 中创建 DiskEncryptionSet 的标识。 如果在运行以下命令时收到类似于“找不到 Active Directory 对象”的错误，请等待几分钟，然后重试。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>后续步骤

创建并配置这些资源之后，可以使用它们来保护托管磁盘。 以下链接包含示例脚本，每个脚本都有各自的方案，可用于保护托管磁盘。

- [Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [使用服务器端加密启用客户管理的密钥-示例](disks-enable-customer-managed-keys-cli.md#examples)