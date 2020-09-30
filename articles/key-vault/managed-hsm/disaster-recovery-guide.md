---
title: 发生影响托管 HSM 的 Azure 服务中断时该怎么办 - Azure Key Vault | Microsoft Docs
description: 了解发生影响托管 HSM 的 Azure 服务中断时该怎么办。
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 13f62631e4913434699f4c5dd5eb1956ca3e3a36
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992150"
---
# <a name="managed-hsm-disaster-recovery"></a>托管 HSM 灾难恢复

如果由于以下任何原因导致原始 HSM 丢失或不可用，你可能希望创建精确的 HSM 副本：

- 原始 HSM 被删除，然后被清除。
- 该区域的灾难性故障已导致所有成员分区遭到破坏。

如果具备以下条件，你可以在相同或不同区域中重新创建 HSM 实例：
- 源 HSM 的[安全域](security-domain.md)。
- 加密安全域的私钥（至少达到仲裁数）。
- 来自源 HSM 的最新完整 HSM [备份](backup-restore.md)。

以下是灾难恢复过程的步骤：

1. 新建 HSM 实例。
1. 激活“安全域恢复”。 将为安全域传输生成一个新的 RSA 密钥对（安全域交换密钥）并作为响应发送，该密钥对将以 SecurityDomainExchangeKey（公共密钥）的形式下载。
1. 创建并上传“安全域传输文件”。 你将需要用于加密安全域的私钥。 私钥在本地使用，在此过程中绝不会转移到任何位置。
1. 获取新 HSM 的备份。 进行任何还原之前都需要备份（即使 HSM 为空）。 使用备份能够轻松地实现回滚。
1. 从源 HSM 还原最近的 HSM 备份

密钥保管库的内容会在区域中复制，并且会复制到至少 150 英里以外（但位于同一个地理位置）的次要区域。 此功能可以保持密钥和机密的高持久性。 有关特定区域对的详细信息，请参阅 [Azure 配对区域](../../best-practices-availability-paired-regions.md)一文。

## <a name="create-a-new-managed-hsm"></a>新建托管 HSM

使用 `az keyvault create` 命令创建托管 HSM。 此脚本包含 3 个必需参数：资源组名称、HSM 名称和地理位置。

必须提供以下输入才能创建托管 HSM 资源：

- HSM 的名称。
- 要将其放置在订阅中的资源组。
- Azure 位置。
- 初始管理员的列表。

以下示例会在资源组 ContosoResourceGroup（位于美国东部 2 位置）创建一个名为 ContosoMHSM 的 HSM，并将当前已登录的用户设为唯一管理员   。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> 运行 create 命令可能需要几分钟的时间。 它成功返回输出后，便可激活 HSM。

此命令的输出会显示创建的托管 HSM 的属性。 两个最重要的属性是：

* **名称**：在本示例中，名称为 ContosoMHSM。 将在其他 Key Vault 命令中使用此名称。
* **hsmUri**：在本示例中，URI 为 https://contosohsm.managedhsm.azure.net 。 通过其 REST API 使用 HSM 的应用程序必须使用此 URI。

Azure 帐户现已获得授权，可在此托管 HSM 上执行任何作业。 到目前为止，尚未授权其他任何人。

## <a name="activate-the-security-domain-recovery-mode"></a>激活“安全域恢复”模式

在正常的创建过程中，此时需要进行初始化并下载新的安全域。 但由于正在执行灾难恢复过程，我们需要 HSM 进入安全域恢复模式并下载安全域交换密钥。 安全域交换密钥是 RSA 公钥，用于在将安全域上传到 HSM 之前对其进行加密。 相应的私钥在 HSM 中受到保护，以确保安全域内容在传输过程中是安全的。

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>将安全域上传到目标 HSM

在本步骤中，需要具备以下项：
- 在上一步中下载的安全域交换密钥。
- 源 HSM 的安全域。
- 曾用于加密安全域的私钥（至少达到仲裁数）。

`az keyvault security-domain upload` 命令执行以下操作：

- 使用你提供的私钥解密源 HSM 的安全域。 
- 创建一个使用在上一步中下载的安全域交换密钥加密的安全域上传 blob，
- 然后将安全域上传 blob 上传到 HSM 以完成安全域恢复

在下面的示例中，我们使用来自 ContosoMHSM 的安全域（对应私钥中的 2），并将其上传到 ContosoMHSM2（正在等待接收安全域） 。 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

现在，源 HSM (ContosoMHSM) 和目标 HSM (ContosoMHSM2) 具有相同的安全域。 我们可以立即将完整备份从源 HSM 还原到目标 HSM。

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>创建新 HSM 的备份（作为还原点）

建议在执行完整的 HSM 还原之前，始终获取完整的备份，这样可以获得一个还原点，以防还原出现问题。

若要创建 HSM 备份，需要具备以下项
- 用于存储备份的存储帐户
- 此存储帐户中的 Blob 存储容器，备份过程将在其中新建文件夹，用于存储加密备份

在下面的示例中，我们对存储帐户 ContosoBackup 的存储容器 mhsmbackupcontainer 中的 HSM 备份使用 `az keyvault backup` 命令 。 我们创建将在 30 分钟后过期的 SAS 令牌，并将其提供给托管 HSM 以写入备份。

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>从源 HSM 还原备份

在本步骤中，需要具备以下项：

- 其中存储了源 HSM 备份的存储帐户和 Blob 容器。
- 要从其中还原备份的文件夹的名称。 如果创建常规备份，此容器中将产生许多文件夹。


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

现在，你已完成了完整的灾难恢复过程。 进行备份时，源 HSM 的内容将复制到目标 HSM，包括所有密钥、版本、属性、标记和角色分配。

## <a name="next-steps"></a>后续步骤

- 若要详细了解安全域，请参阅[关于托管 HSM 安全域](security-domain.md)
- 遵循[托管 HSM 最佳做法](best-practices.md)
