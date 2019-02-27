---
title: 在 Azure Site Recovery 中为启用了 Azure 磁盘加密 (ADE) 的 VM 配置复制 | Microsoft Docs
description: 本文介绍了如何使用 Site Recovery 为 ADE VM 配置从一个 Azure 区域到另一个区域的复制。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: f9abc6d79bd821ef612e9e7648b1b5af98bb5cf6
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456225"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>将启用了 Azure 磁盘加密 (ADE) 的虚拟机复制到另一个 Azure 区域

本文介绍了如何为启用了 Azure 磁盘加密 (ADE) 的 VM 启用从一个 Azure 区域到另一个区域的复制。

>[!NOTE]
>Azure Site Recovery 目前仅支持运行 Windows OS 且[已使用 Azure AD 应用启用加密](https://aka.ms/ade-aad-app)的 Azure VM。
>

## <a name="required-user-permissions"></a>所需的用户权限
Azure Site Recovery 要求用户拥有在目标区域创建密钥保管库以及将密钥复制到该区域的权限。

若要从门户启用 ADE VM 的复制，用户应当具有以下权限。
- 密钥保管库权限
    - list
    - 创建
    - 获取

-   密钥保管库机密权限
    - 列出
    - 创建
    - 获取

- 密钥保管库密钥权限（只有当 VM 使用“密钥加密密钥”来加密磁盘加密密钥时才需要）
    - 列出
    - 获取
    - 创建
    - 加密
    - 解密

可以通过以下方式管理权限：在门户中导航到密钥保管库资源，并为用户添加所需的权限。 例如：以下分步指南演示如何为密钥保管库“ContosoWeb2Keyvault”（位于源区域中）启用它。


-  导航到“主文件夹”>“Keyvaults”>“ContosoWeb2KeyVault”>“访问策略”

![keyvault 权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)



- 可以看到没有任何用户权限，因此，单击“添加新权限”以及用户和权限，添加上面提到的权限

![keyvault 权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果启用灾难恢复 (DR) 的用户没有必需的权限来复制密钥，则可以将以下脚本提供给具有相应权限的安全管理员来将加密机密和密钥复制到目标区域。

如需了解如何进行权限问题故障排除，请参阅[这篇文章](#trusted-root-certificates-error-code-151066)。
>[!NOTE]
>若要从门户启用 ADE VM 的复制，至少需要对密钥保管库、机密和密钥的“列出”权限
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>使用 PowerShell 脚本将 ADE 密钥复制到 DR 区域

1. 通过单击[此链接](https://aka.ms/ade-asr-copy-keys-code)在浏览器窗口中打开“CopyKeys”原始脚本代码。
2. 将该脚本复制到一个文件并将其命名为“Copy-keys.ps1”。
2. 打开 Windows PowerShell 应用程序并转到该文件所在的文件夹位置。
3. 启动“Copy-keys.ps1”
4. 提供 Azure 登录凭据。
5. 选择你的 VM 的 **Azure 订阅**。
6. 等待资源组加载，然后选择你的 VM 的**资源组**。
7. 从所显示的 VM 列表中选择 VM。 列表中只会显示启用了 Azure 磁盘加密的 VM。
8. 选择**目标位置**。
9. **磁盘加密密钥保管库**：默认情况下，Azure Site Recovery 会在目标区域中创建新的密钥保管库，其名称具有基于源 VM 磁盘加密密钥的“asr”后缀。 如果 Azure Site recovery 创建的密钥保管库已存在，则会重复使用。 如果需要，可以从列表中选择一个不同的密钥保管库。
10. **密钥加密密钥保管库**：默认情况下，Azure Site Recovery 会在目标区域中创建新的密钥保管库，其名称具有基于源 VM 密钥加密密钥的“asr”后缀。 如果 Azure Site recovery 创建的密钥保管库已存在，则会重复使用。 如果需要，可以从列表中选择一个不同的密钥保管库。

## <a name="enable-replication"></a>启用复制

此过程假设主要 Azure 区域是东亚，次要区域是东南亚。

1. 在保管库中，单击“+复制”。
2. 指定以下字段：
    - **源**：VM 的起始点，在本例中为 Azure。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在本演示中，源位置为“东亚”
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 这可以是存在恢复服务保管库的同一 Azure Active Directory 租户中的任何订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组下的所有 VM 都会在下一步中列出，以供保护。

3. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每台 VM。 只能选择可以启用复制的计算机。 然后单击“确定”。

4. 在“设置”中，可以选择性地配置目标站点设置：

    - **目标位置**：要在其中复制源虚拟机数据的位置。 根据所选的计算机位置，Site Recovery 会提供合适目标区域的列表。 我们建议将目标位置与恢复服务保管库位置保持相同。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅将与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Azure Site Recovery 会在目标位置中创建一个名称带有“asr”后缀的新资源组。 如果 Azure Site recovery 创建的资源组已存在，则会重复使用。 还可以选择对其进行自定义，如以下部分中所示。 目标资源组的位置可以是除托管源虚拟机区域以外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 会在目标区域中创建一个名称带有“asr”后缀的新虚拟网络。 这会映射到源网络并会用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户(如果源 VM 不使用托管磁盘)**：默认情况下，Site Recovery 会创建模拟源 VM 存储配置的新目标存储帐户。 如果存储帐户已存在，则重复使用。
    - **副本托管磁盘(如果源 VM 使用托管磁盘)**：Site Recovery 在目标区域中创建新的副本托管磁盘，以生成和源 VM 的托管磁盘存储类型一致（标准或高级）的镜像磁盘。
    - **缓存存储帐户**：Site Recovery 需要源区域中称为缓存存储的额外存储帐户。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。
    - **可用性集**：默认情况下，Azure Site Recovery 会在目标区域中创建一个名称带有“asr”后缀的新可用性集。 如果 Azure Site Recovery 创建的可用性集已存在，则会重复使用。
    - **磁盘加密密钥保管库**：默认情况下，Azure Site Recovery 会在目标区域中创建新的密钥保管库，其名称具有基于源 VM 磁盘加密密钥的“asr”后缀。 如果 Azure Site recovery 创建的密钥保管库已存在，则会重复使用。
    - **密钥加密密钥保管库**：默认情况下，Azure Site Recovery 会在目标区域中创建新的密钥保管库，其名称具有基于源 VM 密钥加密密钥的“asr”后缀。 如果 Azure Site recovery 创建的密钥保管库已存在，则会重复使用。
    - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 会使用恢复点保留期为“24 小时”、应用一致性快照频率为“60 分钟”的默认设置创建新的复制策略。



## <a name="customize-target-resources"></a>自定义目标资源

可以修改 Site Recovery 使用的默认目标设置。


1. 单击“目标订阅”旁边的“自定义：”，修改默认目标订阅。 从同一 Azure Active Directory (AAD) 租户中可用的所有订阅列表中选择订阅。

2. 单击“资源组、网络、存储和可用性集”旁边的“自定义:”可修改以下默认设置：
    - 在“目标资源组”中，从订阅内目标位置中的所有资源组列表中选择资源组。
    - 在“目标虚拟网络”中，从目标位置中所有虚拟网络列表中选择网络。
    - 在“可用性集”中，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。
    - 在“目标存储帐户”中，选择要使用的帐户。


2. 单击“加密设置”旁边的“自定义:”来修改以下默认设置：
    - 在“目标磁盘加密密钥保管库”中，从订阅的目标位置中的所有密钥保管库的列表中选择目标磁盘加密密钥保管库。
  - 在“目标密钥加密密钥保管库”中，从订阅的目标位置中的所有密钥保管库的列表中选择目标密钥加密密钥保管库。

3. 单击“创建目标资源” > “启用复制”。
4. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示进度。 单击“刷新”按钮可获取最新状态。
>

## <a name="update-target-vm-encryption-settings"></a>更新目标 VM 加密设置
在以下场景中，将要求你更新目标 VM 加密设置。
  - 你在 VM 上启用了 Site Recovery 复制，并在之后又在源 VM 上启用了 Azure 磁盘加密 (ADE)
  - 你在 VM 上启用了 Site Recovery 复制，并在之后又在源 VM 上启用了磁盘加密密钥和/或密钥加密密钥

可以使用[此脚本](#copy-ade-keys-to-dr-region-using-powershell-script)将加密密钥复制到目标区域，然后在“恢复服务保管库”->“复制的项”->“属性”->“计算和网络”中更新目标加密设置。

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="trusted-root-certificates-error-code-151066"></a>Azure 到 Azure VM 复制期间对密钥保管库权限问题进行故障排除

**原因 1：** 可能从目标区域选择了不具有所需权限的已创建的密钥保管库。
如果在目标区域中选择已创建的密钥保管库，而不是让 Azure Site Recovery 创建它。 请确保密钥保管库具有上文所述的所需权限。</br>
例如：用户尝试复制 VM，其在源区域中拥有名为“ContososourceKeyvault”的密钥保管库。
用户拥有源区域密钥保管库的所有权限，但他在保护期间选择了已创建的密钥保管库“ContosotargetKeyvault”，该密钥保管库没有权限，因此保护将会引发错误。</br>
**如何修复：** 转到“主文件夹”>“Keyvaults”>“ContososourceKeyvault”>“访问策略”并添加权限，如上所示。 

**原因 2：** 可能从目标区域选择了不具有解密/加密权限的已创建的密钥保管库。
如果在目标区域中选择已创建的密钥保管库，而不是让 Azure Site Recovery 创建它。 请确保用户具有解密/加密权限，以防还需要在源区域中加密密钥。</br>
例如：用户尝试复制 VM，其在源区域中拥有名为“ContososourceKeyvault”的密钥保管库。
用户拥有源区域密钥保管库的所有权限，但他在保护期间选择了已创建的密钥保管库“ContosotargetKeyvault”，该密钥保管库没有解密和加密权限。</br>
**如何修复：** 转到“主文件夹”>“Keyvaults”>“ContososourceKeyvault”>“访问策略”，并在“密钥权限”>“加密操作”下添加权限。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
