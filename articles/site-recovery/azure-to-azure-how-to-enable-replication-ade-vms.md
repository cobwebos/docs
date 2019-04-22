---
title: 在 Azure Site Recovery 中配置 Azure 磁盘加密启用 Vm 复制 |Microsoft Docs
description: 本文介绍如何使用 Site Recovery 中配置已启用 Azure 磁盘加密的 Vm 从一个 Azure 区域到另一个的复制。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678949"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>启用 Azure 磁盘加密的虚拟机复制到另一个 Azure 区域

本文介绍如何启用 Azure 磁盘加密的 Vm 从一个 Azure 区域复制到另一个。

>[!NOTE]
>Azure Site Recovery 当前仅支持 Azure Vm 运行 Windows OS 的且[以便使用 Azure Active Directory (Azure AD) 进行加密启用](https://aka.ms/ade-aad-app)。

## <a name="required-user-permissions"></a>所需的用户权限
站点恢复需要用户具有在目标区域和复制密钥的区域中创建密钥保管库的权限。

若要启用从 Azure 门户中启用磁盘加密的 Vm 的复制，用户都需要以下权限：

- 密钥保管库权限
    - 列出
    - 创建
    - 获取

-   密钥保管库机密权限
    - 列出
    - 创建
    - 获取

- 密钥保管库密钥权限 （在 Vm 使用密钥加密密钥来加密磁盘加密密钥时才需要）
    - 列出
    - 获取
    - 创建
    - 加密
    - 解密

若要管理权限，请转到在门户中的密钥保管库资源。 添加所需的权限的用户。 下面的示例演示如何启用对密钥保管库的权限*ContosoWeb2Keyvault*，这是在源区域中。

1. 转到**主页** > **key Vault** > **ContosoWeb2KeyVault > 访问策略**。

   ![密钥保管库权限窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 您可以看到不有任何用户权限。 选择“添加新订阅”。 输入用户和权限信息。

   ![密钥保管库权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果，让灾难恢复 (DR) 的用户没有权限来复制密钥，具有适当权限的安全管理员可以使用以下脚本以将加密机密和密钥复制到目标区域。

若要对权限进行故障排除，请参阅[密钥保管库权限问题](#trusted-root-certificates-error-code-151066)这篇文章中更高版本。

>[!NOTE]
>若要启用的门户中启用磁盘加密的 Vm 复制，您需要至少"列出"密钥保管库、 机密和密钥的权限。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 脚本将磁盘加密密钥复制到 DR 区域

1. [打开"CopyKeys"原始脚本代码](https://aka.ms/ade-asr-copy-keys-code)。
2. 将脚本复制到文件，并将其命名**复制 keys.ps1**。
3. 打开 Windows PowerShell 应用程序，并转到保存该文件的文件夹。
4. 执行复制 keys.ps1。
5. 提供 Azure 凭据登录。
6. 选择你的 VM 的 **Azure 订阅**。
7. 等待加载，请将资源组，然后选择**资源组**的 Vm。
8. 从显示的列表中选择的 Vm。 为磁盘加密启用的 Vm 位于列表中。
9. 选择**目标位置**。

    - **磁盘加密密钥保管库**
    - **密钥加密的密钥保管库**

   默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 保管库的名称已根据源 VM 磁盘加密密钥"asr"后缀。 如果密钥保管库已存在已创建的 Site Recovery，将重复使用它。 从列表中根据需要选择不同的密钥保管库。

## <a name="enable-replication"></a>启用复制

对于此示例中，主要 Azure 区域是东亚，和次要区域是东南亚。

1. 在保管库中，选择 **+ 复制**。
2. 请注意以下字段。
    - **源**：VM 的起始点，在本例中为 Azure。
    - **源位置**：你想要保护虚拟机的 Azure 区域。 对于此示例中，源位置是"亚洲东部"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是与恢复服务保管库相同的 Azure Active Directory 租户中的任何订阅。
    - **资源组**：源虚拟机所属的资源组。 为保护下一步中列出了所选的资源组中的所有 Vm。

3. 在中**虚拟机** > **选择虚拟机**，选择想要复制的每个 VM。 只能选择可以启用复制的计算机。 选择“确定”。

4. 在中**设置**，可以配置以下目标站点设置。

    - **目标位置**：要在其中复制源虚拟机数据的位置。 Site Recovery 提供基于所选的计算机的位置的合适目标区域的列表。 我们建议为恢复服务保管库的位置使用相同的位置。
    - **目标订阅**：在用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Site Recovery 在目标区域中创建新的资源组。 名称获取"asr"后缀。 如果资源组已存在的已通过 Azure Site Recovery，将重复使用它。 您还可以进行自定义，如以下部分中所示。 目标资源组的位置可以是除托管源虚拟机的区域的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 在目标区域中创建新的虚拟网络。 名称获取"asr"后缀。 它映射到源网络并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户 （如果您的源 VM 不使用托管磁盘）**:默认情况下，Site Recovery 通过模拟源 VM 存储配置创建新的目标存储帐户。 如果存储帐户已存在，将重复使用它。
    - **托管磁盘副本 （如果源 VM 使用托管的磁盘）**:Site Recovery 在目标区域，以反映源 VM 的托管的磁盘的相同存储类型 （标准或高级） 作为源 VM 的托管磁盘创建新副本托管磁盘。
    - **缓存存储帐户**：Site Recovery 需要额外的存储帐户名为*缓存存储*源区域中。 源 Vm 上的所有更改跟踪并发送到缓存存储帐户。 它们随后要复制到目标位置。
    - **可用性集**：默认情况下，Site Recovery 会创建新的可用性集在目标区域中。 名称具有"asr"后缀。 如果已创建的 Site Recovery 的可用性集存在，将重复使用它。
    - **磁盘加密密钥保管库**：默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 它具有对源 VM 磁盘加密密钥基于"asr"后缀。 如果存在已创建的 Azure Site Recovery 的密钥保管库，它将重复使用。
    - **密钥加密密钥保管库**：默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 名称已根据源 VM 的密钥加密密钥"asr"后缀。 如果存在已由 Azure Site Recovery 创建的密钥保管库，它将重复使用。
    - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 将使用的默认设置创建新的复制策略*24 小时*恢复点保留期并*60 分钟*应用一致性快照频率。

## <a name="customize-target-resources"></a>自定义目标资源

请按照下列步骤来修改站点恢复默认目标设置。

1. 选择**自定义**旁边"目标订阅"来修改默认目标订阅。 从列表中的 Azure AD 租户中可用的订阅选择的订阅。

2. 选择**自定义**旁边"资源组、 网络、 存储和可用性集"来修改以下默认设置：
    - 有关**目标资源组**，从订阅中的目标位置中的资源组列表中选择资源组。
    - 有关**目标虚拟网络**，从目标位置中的虚拟网络的列表中选择网络。
    - 有关**可用性集**，可以添加可用性集设置到该 VM，如果它们是源区域中的可用性集的一部分。
    - 有关**目标存储帐户**，选择要使用的帐户。

2. 选择**自定义**旁边"加密设置"来修改以下默认设置：
   - 有关**目标磁盘加密密钥保管库**，从订阅中的目标位置中的密钥保管库的列表中选择目标磁盘加密密钥保管库。
   - 有关**目标密钥加密密钥保管库**，从订阅中的目标位置中的密钥保管库的列表中选择目标密钥加密密钥保管库。

3. 选择**创建目标资源** > **启用复制**。
4. 为 Vm 启用复制后，可以检查 Vm 的运行状况状态下**复制的项**。

>[!NOTE]
>在初始复制期间状态可能需要一些时间来刷新，但不显示明显的进度。 单击**刷新**以获取最新状态。

## <a name="update-target-vm-encryption-settings"></a>更新目标 VM 加密设置
在以下情况下，你将需要更新的目标 VM 加密设置：
  - 启用 VM 上的 Site Recovery 复制。 更高版本，则启用对源 VM 的磁盘加密。
  - 启用 VM 上的 Site Recovery 复制。 更高版本，你更改磁盘加密密钥或密钥加密密钥源 VM 上。

可以使用[脚本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)若要将加密密钥复制到目标区域，然后更新中的目标加密设置**恢复服务保管库** > *复制项* > **属性** > **计算和网络**。

![更新 ADE 设置对话框窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Azure 到 Azure VM 复制期间对密钥保管库权限问题进行故障排除

**原因 1：** 你可能已选择从目标区域的已创建密钥保管库不具有所需的权限，而不是让 Site Recovery 创建一个。 请确保具有密钥保管库需要的权限，如前面所述。

例如：尝试复制了密钥保管库的 VM *ContososourceKeyvault*上源区域。
在源区域的 key vault 上具有所有权限。 但在保护，选择已创建 key vault ContosotargetKeyvault，其不具有权限。 出现错误。

**如何修复：** 转到**主页** > **key Vault** > **ContososourceKeyvault** > **访问策略**并添加适当的权限。

**原因 2：** 您可能会选择从目标区域不具有一个已创建的 key vault 解密加密而不是让 Site Recovery 创建一个权限。 请确保你拥有解密加密权限，如果也要加密在源区域的密钥。</br>

例如：尝试复制了密钥保管库的 VM *ContososourceKeyvault*上源区域。 在源区域的 key vault 上具有所需的所有权限。 但在保护，选择已创建 key vault ContosotargetKeyvault，没有要解密和加密的权限。 出现错误。</br>

**如何修复：** 转到**主页** > **key Vault** > **ContososourceKeyvault** > **访问策略**。 添加权限下的**密钥权限** > **加密操作**。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
