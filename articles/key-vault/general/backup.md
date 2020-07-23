---
title: Azure Key Vault 备份 | Microsoft Docs
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
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156282"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault 备份

本文档将向你演示如何备份存储在密钥保管库中的单个机密、密钥和证书。 此备份旨在为你提供所有机密的脱机副本，以防你无法访问密钥保管库。

## <a name="overview"></a>概述

Key Vault 自动提供了几个功能来维护可用性并防止数据丢失。 仅当存在关键业务理由来维护机密备份时，才应尝试此备份。 备份密钥保管库中的机密可能会带来额外的操作难题，例如在机密过期或轮换时维护多组日志、权限和备份。

Key Vault 在灾难情况下维持可用性，并将请求自动故障转移到配对区域，而无需用户进行任何干预。 有关详细信息，请参阅以下链接。 [Azure Key Vault 灾难恢复](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault 通过软删除和清除保护防止意外和恶意删除机密。 如果要防止意外或恶意删除机密，请在密钥保管库上配置软删除和清除保护功能。 有关详细信息，请参阅以下文档。 [Azure Key Vault 恢复](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>限制

Azure Key Vault 当前不支持在单个操作中备份整个密钥保管库的方法。 Microsoft 或 Azure Key Vault 团队不支持使用此文档中列出的命令执行密钥保管库自动备份的任何尝试。

尝试使用以下文档中显示的命令创建自定义自动化可能会导致错误。

* 使用多个版本备份机密可能会导致超时错误。
* 备份创建时间点快照。 在备份期间机密可能会续订，从而导致加密密钥不匹配。
* 超过每秒请求的密钥保管库服务限制将导致密钥保管库受到限制，并导致备份失败。

## <a name="design-considerations"></a>设计注意事项

备份存储在密钥保险库（机密、密钥或证书）中的对象时，备份操作会将该对象作为加密的 blob 下载。 无法在 Azure 外部解密此 blob。 若要从此 blob 获取可用数据，必须将 blob 还原到同一 Azure 订阅和 Azure 地理位置内的密钥保管库中。
[Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅的参与者级别或更高权限
* 包含要备份的机密的主密钥保管库
* 将在其中还原机密的辅助密钥保管库。

## <a name="back-up-and-restore-with-azure-portal"></a>使用 Azure 门户备份和还原

### <a name="back-up"></a>备份

1. 导航到 Azure 门户。
2. 选择密钥保管库。
3. 导航到要备份的对象（机密、密钥或证书）。

    ![映像](../media/backup-1.png)

4. 选择对象。
5. 选择“下载备份”

    ![映像](../media/backup-2.png)
    
6. 单击“下载”按钮。

    ![映像](../media/backup-3.png)
    
7. 将加密的 blob 存储在安全的位置。

### <a name="restore"></a>还原

1. 导航到 Azure 门户。
2. 选择密钥保管库。
3. 导航到要还原的对象类型（机密、密钥或证书）。
4. 选择“还原备份”

    ![映像](../media/backup-4.png)
    
5. 浏览到存储已加密 blob 的位置。
6. 选择“确定”。

## <a name="back-up-and-restore-with-the-azure-cli"></a>使用 Azure CLI 备份和还原

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>后续步骤

启用 Azure Key Vault 的日志记录和监视。 [Azure 密钥保管库日志记录](https://docs.microsoft.com/azure/key-vault/general/logging)
