---
title: 备份存储在 Azure 密钥保管库中的机密、密钥或证书 | Microsoft Docs
description: 使用此文档可帮助备份存储在 Azure Key Vault 中的机密、密钥或证书。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 668154b5e54ed4d496d272e33e8fc7f378e75e8a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386259"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault 备份

本文档介绍如何备份密钥保管库中存储的机密、密钥和证书。 备份旨在为你提供所有机密的脱机副本，以防你无法访问密钥保管库。

## <a name="overview"></a>概述

Azure 密钥保管库自动提供了一些功能来帮助你维护可用性并防止数据丢失。 仅当有充分且重要的业务理由时才备份机密。 备份密钥保管库中的机密可能会带来操作难题，例如在机密过期或轮换时维护多组日志、权限和备份。

密钥保管库在灾难情况下维持可用性，并将请求自动故障转移到配对区域，而无需用户进行任何干预。 有关详细信息，请参阅 [Azure Key Vault 可用性和冗余](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)。

如果要防止意外或恶意删除机密，请在密钥保管库上配置软删除和清除保护功能。 有关详细信息，请参阅 [Azure 密钥保管库软删除概述](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)。

## <a name="limitations"></a>限制

Azure 密钥保管库当前不提供在单个操作中备份整个密钥保管库的方法。 任何使用此文档中列出的命令执行密钥保管库自动备份的尝试都可能导致错误，且 Microsoft 或 Azure 密钥保管库团队不支持此操作。 

另请考虑以下后果：

* 备份具有多个版本的机密可能导致超时错误。
* 备份会创建时间点快照。 在备份期间机密可能会续订，从而导致加密密钥不匹配。
* 如果超过每秒请求的密钥保管库服务限制，密钥保管库将受到限制，备份将失败。

## <a name="design-considerations"></a>设计注意事项

备份密钥保管库对象（如机密、密钥或证书）时，备份操作会将该对象作为加密的 blob 下载。 无法在 Azure 外部解密此 blob。 若要从此 blob 获取可用数据，必须将 blob 还原到同一 Azure 订阅和 [地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)内的密钥保管库中。

## <a name="prerequisites"></a>先决条件

若要备份密钥保管库对象，你必须具有： 

* Azure 订阅的参与者级别或更高权限。
* 包含要备份的机密的主密钥保管库。
* 将在其中还原机密的辅助密钥保管库。

## <a name="back-up-and-restore-from-the-azure-portal"></a>从 Azure 门户备份和还原

按照本部分中的步骤使用 Azure 门户来备份和还原对象。

### <a name="back-up"></a>备份

1. 转到 Azure 门户。
2. 选择密钥保管库。
3. 转到要备份的对象（机密、密钥或证书）。

    ![显示应在密钥保管库中的什么位置选择“密钥”设置和对象的屏幕截图。](../media/backup-1.png)

4. 选择对象。
5. 选择“下载备份”。

    ![显示应在密钥保管库中的什么位置选择“下载备份”按钮的屏幕截图。](../media/backup-2.png)
    
6. 选择“下载”。

    ![显示应在密钥保管库中的什么位置选择“下载”按钮的屏幕截图。](../media/backup-3.png)
    
7. 将加密的 blob 存储在安全的位置。

### <a name="restore"></a>还原

1. 转到 Azure 门户。
2. 选择密钥保管库。
3. 转到要还原的对象类型（机密、密钥或证书）。
4. 选择“还原备份”。

    ![显示应在密钥保管库中的什么位置选择“还原备份”的屏幕截图。](../media/backup-4.png)
    
5. 转到加密的 blob 的存储位置。
6. 选择“确定”。

## <a name="back-up-and-restore-from-the-azure-cli"></a>从 Azure CLI 备份和还原

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>后续步骤

启用 Key Vault 的[日志记录和监视](https://docs.microsoft.com/azure/key-vault/general/logging)。
