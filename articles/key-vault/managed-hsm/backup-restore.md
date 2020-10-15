---
title: Azure 托管 HSM 的完整备份/还原和选择性还原
description: 本文档介绍完整备份/还原和选择性还原
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 3d999375d746bb359acdccf9bf48f8b77d509776
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90992154"
---
# <a name="full-backup-and-restore"></a>完整备份和还原

> [!NOTE]
> 此功能仅适用于资源类型托管 HSM。

托管 HSM 支持创建 HSM 全部内容（包括所有密钥、版本、属性、标记和角色分配）的完整备份。 备份使用与 HSM 的安全域关联的加密密钥进行加密。

备份是一种数据平面操作。 启动备份操作的调用方必须具有执行 dataAction“Microsoft.KeyVault/managedHsm/backup/start/action”的权限。

只有以下内置角色具有执行完整备份的权限：
- 托管 HSM 管理员
- 托管 HSM 备份

若要执行完整备份，必须提供以下信息：
- HSM 名称或 URL
- 存储帐户名称
- 存储帐户 blob 存储容器
- 具有权限 `crdw` 的存储容器 SAS 令牌

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>完整备份

备份是长期操作，但会立即返回作业 ID。 可使用此作业 ID 检查备份过程的状态。 备份过程会在指定容器中创建一个文件夹，该文件夹具有以下命名模式 `mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`，其中 HSM_NAME 是要备份的托管 HSM 的名称，而 YYYY、MM、DD、HH、MM、mm 和 SS 是收到备份命令时 UTC 日期/时间的年、月、日、时、分和秒。

在备份过程中，HSM 可能不会以完整的吞吐量运行，因为某些 HSM 分区将忙于执行备份操作。

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>完整还原

完整还原支持使用以前的备份完整还原 HSM 的内容，包括所有密钥、版本、属性、标记和角色分配。 当前存储在 HSM 中的所有内容都将被擦除，HSM 将返回到创建源备份时所处的状态。

> [!IMPORTANT]
> 完整还原是一种破坏性很强的操作。 因此，必须在执行 `restore` 操作前的 30 分钟内完成完整备份。

还原是一种数据平面操作。 启动还原操作的调用方必须具有执行 dataAction“Microsoft.KeyVault/managedHsm/restore/start/action”的权限。 在其中创建备份的源 HSM 以及将在其中执行还原的目标 HSM 必须具有相同的安全域。 详细了解[托管 HSM 安全域](security-domain.md)。

若要执行完整还原，必须提供以下信息：
- HSM 名称或 URL
- 存储帐户名称
- 存储帐户 blob 容器
- 具有权限 `rl` 的存储容器 SAS 令牌
- 存储源备份所在的存储容器文件夹名称

还原是长期操作，但会立即返回作业 ID。 可使用此作业 ID 检查还原过程的状态。 在还原过程中，HSM 将进入还原模式，所有数据平面命令（除检查还原状态之外）都将禁用。

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>备份 HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>后续步骤
- 请参阅[使用 Azure CLI 管理托管 HSM](key-management.md)。
- 详细了解[托管 HSM 安全域](security-domain.md)