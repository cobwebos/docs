---
title: 在 Azure Site Recovery 中为启用了 Azure 磁盘加密的 Vm 配置复制 |Microsoft Docs
description: 本文介绍如何使用 Site Recovery 对启用了 Azure 磁盘加密的 VM 配置从一个 Azure 区域到另一个区域的 Azure VM 复制。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 5e5ae11a00b2a3656deceeeaf928536238a325fc
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840633"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>将启用了 Azure 磁盘加密的虚拟机复制到另一个 Azure 区域

本文介绍如何将启用了 Azure 磁盘加密的 VM 从一个 Azure 区域复制到另一个 Azure 区域。

>[!NOTE]
>Azure Site Recovery 当前仅支持运行 Windows 操作系统的 Azure Vm。

## <a id="required-user-permissions"></a>必需的用户权限
Site Recovery 要求用户有权在目标区域中创建密钥保管库, 并将密钥从源区域密钥保管库复制到目标区域密钥保管库。

若要启用从 Azure 门户复制启用磁盘加密的 Vm, 用户需要在**源区域和目标区域**密钥保管库中具有以下权限。

- 密钥保管库权限
    - 列出、创建和获取
    
- 密钥保管库机密权限
    - 机密管理操作
        - 获取、列出和设置
    
- Key Vault 密钥权限（只有当 VM 使用“密钥加密密钥”来加密磁盘加密密钥时才需要）
    - 密钥管理操作
        - 获取、列出和创建
    - 加密操作
        - 解密和加密

若要管理权限，请在门户中转到 Key Vault 资源。 添加用户所需的权限。 以下示例演示如何启用对源区域中 Key Vault *ContosoWeb2Keyvault* 的权限。

1. 转到“主页” > “Keyvaults” > “ContosoWeb2KeyVault”>“访问策略”。

   ![“Key Vault 权限”窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 可以发现目前没有任何用户权限。 选择“添加新订阅”。 输入用户和权限信息。

   ![keyvault 权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果启用灾难恢复 (DR) 的用户无权复制密钥，则拥有相应权限的安全管理员可使用以下脚本将加密机密和密钥复制到目标区域。

若要排查权限问题，请参阅本文稍后所述的 [Key Vault 权限问题](#trusted-root-certificates-error-code-151066)。

>[!NOTE]
>若要通过门户对启用了磁盘加密的 VM 启用复制，至少需要对 Key Vault、机密和密钥拥有“列出”权限。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 脚本将磁盘加密密钥复制到 DR 区域

1. [打开“CopyKeys”原始脚本代码](https://aka.ms/ade-asr-copy-keys-code)。
2. 将该脚本复制到一个文件并将其命名为 **Copy-keys.ps1**。
3. 打开 Windows PowerShell 应用程序，并转到该文件所保存到的文件夹。
4. 执行 Copy-keys.ps1。
5. 提供用于登录的 Azure 凭据。
6. 选择你的 VM 的 **Azure 订阅**。
7. 等待资源组加载，然后选择 VM 的**资源组**。
8. 从显示的列表中选择 VM。 该列表只显示启用了磁盘加密的 VM。
9. 选择**目标位置**。

    - **磁盘加密 Key Vault**
    - **密钥加密 Key Vault**

   默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 保管库的名称包含基于源 VM 磁盘加密密钥的“asr”后缀。 如果已存在 Site Recovery 创建的 Key Vault，则会重复使用它。 根据需要从列表中选择不同的 Key Vault。

## <a name="enable-replication"></a>启用复制

在此示例中, 主要的 Azure 区域是东亚的, 次要区域是南部东亚。

1. 在保管库中选择“+复制”。
2. 注意以下字段。
    - **源**：VM 的起始点，在本例中为 Azure。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在此示例中, 源位置为 "东亚"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是恢复服务保管库所在的同一 Azure Active Directory 租户中的任一订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组中要保护的所有 VM 会在下一步骤中列出。

3. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个 VM。 只能选择可以启用复制的计算机。 选择“确定”。

4. 在“设置”中，可以配置以下目标站点设置。

    - **目标位置**：要在其中复制源虚拟机数据的位置。 Site Recovery 根据所选计算机的位置提供合适的目标区域列表。 我们建议使用与恢复服务保管库位置相同的位置。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Site Recovery 会在目标区域中创建一个新的资源组， 其名称带有“asr”后缀。 如果已存在 Azure Site Recovery 创建的资源组，将会重复使用它。 此外，可按以下部分所述，选择对资源组进行自定义。 目标资源组的位置可以是除托管源虚拟机区域以外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 会在目标区域中创建一个新的虚拟网络， 其名称带有“asr”后缀。 此虚拟网络会映射到源网络并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户（如果源 VM 不使用托管磁盘）** ：默认情况下，Site Recovery 会创建模拟源 VM 存储配置的新目标存储帐户。 如果已存在一个存储帐户，将重复使用它。
    - **副本托管磁盘（如果源 VM 使用托管磁盘）** ：Site Recovery 在目标区域新建托管磁盘副本，以生成和源 VM 的托管磁盘存储类型一致（标准或高级）的镜像磁盘。
    - **缓存存储帐户**：Site Recovery 需要源区域中称为“缓存存储”的额外存储帐户。 源 VM 上的所有更改将受到跟踪并发送到缓存存储帐户。 它们随后会复制到目标位置。
    - **可用性集**：默认情况下，Site Recovery 会在目标区域中创建一个新的可用性集， 其名称带有“asr”后缀。 如果已存在 Site Recovery 创建的可用性集，将会重复使用它。
    - **磁盘加密密钥保管库**：默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 其名称包含基于源 VM 磁盘加密密钥的“asr”后缀。 如果已存在 Azure Site Recovery 创建的 Key Vault，将会重复使用它。
    - **密钥加密密钥保管库**：默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 其名称包含基于源 VM 密钥加密密钥的“asr”后缀。 如果已存在 Azure Site Recovery 创建的 Key Vault，将会重复使用它。
    - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 会使用恢复点保留期为 24 小时、应用一致性快照频率为 60 分钟的默认设置创建新的复制策略。

## <a name="customize-target-resources"></a>自定义目标资源

遵循以下步骤修改 Site Recovery 默认目标设置。

1. 选择“目标订阅”旁边的“自定义”以修改默认目标订阅。 从 Azure AD 租户中可用的订阅列表中选择订阅。

2. 选择“资源组、网络、存储和可用性集”旁边的“自定义”，以修改以下默认设置：
    - 对于“目标资源组”，请从订阅目标位置中的资源组列表中选择资源组。
    - 对于“目标虚拟网络”，请从目标位置中的虚拟网络列表中选择网络。
    - 对于“可用性集”，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。
    - 对于“目标存储帐户”，请选择要使用的帐户。

2. 选择“加密设置”旁边的“自定义”，以修改以下默认设置：
   - 对于“目标磁盘加密 Key Vault”，请从订阅的目标位置中的 Key Vault 列表中选择目标磁盘加密 Key Vault。
   - 对于“目标加密加密 Key Vault”，请从订阅的目标位置中的 Key Vault 列表中选择目标密钥加密 Key Vault。

3. 选择“创建目标资源” > “启用复制”。
4. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况。

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示确切的进度。 单击“刷新”可查看最新状态。

## <a name="update-target-vm-encryption-settings"></a>更新目标 VM 加密设置
在以下情况下，需要更新目标 VM 的加密设置：
  - 你已在 VM 上启用 Site Recovery 复制。 后来，你在源 VM 上启用了磁盘加密。
  - 你已在 VM 上启用 Site Recovery 复制。 后来，你在源 VM 上更改了磁盘加密密钥或密钥加密密钥。

可以使用[一个脚本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)将加密密钥复制到目标区域，然后在“恢复服务保管库” > “复制的项” > “属性” > “计算和网络”中更新目标加密设置。

![“更新 ADE 设置”对话框窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Azure 到 Azure VM 复制过程中的密钥保管库权限问题疑难解答

Azure Site Recovery 要求至少具有对源区域密钥保管库的读取权限和对目标区域密钥保管库的写入权限, 才能读取机密并将其复制到目标区域密钥保管库。 

**原因 1：** 你没有**源区域密钥保管库**的 "GET" 权限来读取密钥。 </br>
**如何修复：** 无论你是否是订阅管理员, 都必须拥有对密钥保管库的访问权限, 这一点很重要。

1. 请访问源区域密钥保管库, 此示例中为 "ContososourceKeyvault" >**访问策略** 
2. 在 "**选择主体**添加用户名" 下, 例如:dradmin@contoso.com""
3. 在 "**密钥权限**" 下选择 "获取" 
4. 在 "**机密权限**" 下, 选择 GET 
5. 保存访问策略

**原因 2：** 你没有对**目标区域密钥保管库**所需的权限来写入密钥。 </br>

例如：你尝试复制源区域中包含 Key Vault *ContososourceKeyvault* 的 VM。
你对源区域中的 Key Vault 拥有所有权限。 但在保护期间，你选择了已创建的、但没有权限的 Key Vault ContosotargetKeyvault。 发生错误。

[目标密钥保管库](#required-user-permissions)所需的权限

**如何修复：** 中转到**Home** > **Keyvaults** > ContosotargetKeyvaultAccess > **策略**并添加适当的权限。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
