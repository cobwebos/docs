---
title: 在 Azure Site Recovery 中为加密的 Azure Vm 启用复制
description: 本文介绍如何使用 Site Recovery 为 azure 区域中启用了 Azure 磁盘加密的 Vm 配置复制。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 3a59f137240eff2a3a68fa5547be8c6c25d3e5fe
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772221"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>将启用了 Azure 磁盘加密的虚拟机复制到另一个 Azure 区域

本文介绍如何将 azure 磁盘加密（ADE）启用的 azure Vm 从一个 Azure 区域复制到另一个 Azure 区域。

>[!NOTE]
> Site Recovery 当前支持对运行 Windows 和 Linux 操作系统的 Vm 具有和不包含 Azure Active Directory （AAD）的 ADE。  对于运行 ADE 1.1 （不使用 AAD）的计算机，Vm 必须使用托管磁盘。 不支持包含非托管磁盘的 Vm。 如果从 ADE 0.1 （使用 AAD）切换到1.1，则需要在启用1.1 后禁用复制并为 VM 启用复制。


## <a id="required-user-permissions"></a>必需的用户权限
Site Recovery 要求用户有权在目标区域中创建密钥保管库，并将密钥从源区域密钥保管库复制到目标区域密钥保管库。

若要启用从 Azure 门户复制启用磁盘加密的 Vm，用户需要在**源区域和目标区域**密钥保管库中具有以下权限。

- 密钥保管库权限
    - 列出、创建和获取
    
- 密钥保管库机密权限
    - 机密管理操作
        - 获取、列出和设置
    
- Key vault 密钥权限（仅当 Vm 使用密钥加密密钥加密磁盘加密密钥时才需要）
    - 密钥管理操作
        - 获取、列出和创建
    - 加密操作
        - 解密和加密

若要管理权限，请在门户中转到密钥保管库资源。 为用户添加所需的权限。 下面的示例演示如何对源区域中的密钥保管库*ContosoWeb2Keyvault*启用权限。

1. 请参阅**Home** > **Keyvaults** > **ContosoWeb2KeyVault > 访问策略**。

   ![Key vault 权限窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 你可以看到没有用户权限。 选择“添加新订阅”。 输入用户和权限信息。

   ![Keyvault 权限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果启用灾难恢复（DR）的用户不具有复制密钥的权限，则具有适当权限的安全管理员可以使用以下脚本将加密机密和密钥复制到目标区域。

若要排查权限问题，请参阅本文后面的[密钥保管库权限问题](#trusted-root-certificates-error-code-151066)。

>[!NOTE]
>若要从门户启用启用磁盘加密的 Vm 的复制，需要对密钥保管库、机密和密钥至少具有 "列表" 权限。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 脚本将磁盘加密密钥复制到 DR 区域

1. [打开 "CopyKeys" 原始脚本代码](https://aka.ms/ade-asr-copy-keys-code)。
2. 将脚本复制到一个文件，将其命名为**Copy-keys**。
3. 打开 Windows PowerShell 应用程序，然后前往保存该文件的文件夹。
4. 执行 Copy-keys。
5. 提供用于登录的 Azure 凭据。
6. 选择你的 VM 的 **Azure 订阅**。
7. 等待资源组加载，然后选择 Vm 的**资源组**。
8. 从显示的列表中选择 Vm。 只有为磁盘加密启用的 Vm 才会在列表中列出。
9. 选择**目标位置**。

    - **磁盘加密密钥保管库**
    - **密钥加密密钥保管库**

   默认情况下，Site Recovery 会在目标区域中创建新的 Key Vault， 保管库的名称有一个基于源 VM 磁盘加密密钥的 "asr" 后缀。 如果 Site Recovery 创建的密钥保管库已存在，则会重复使用它。 如有必要，请从列表中选择一个不同的密钥保管库。

## <a name="enable-replication"></a>启用复制

在此示例中，主要的 Azure 区域是东亚的，次要区域是南部东亚。

1. 在保管库中，选择 " **+ 复制**"。
2. 请注意以下字段。
    - **源**：VM 的起始点，在本例中为 **Azure**。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在此示例中，源位置为 "东亚"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是与恢复服务保管库位于同一 Azure Active Directory 租户中的任何订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组中的所有 Vm 都会在下一步中列出，以供保护。

3. 在 "**虚拟机**" > **选择 "虚拟机**"，然后选择要复制的每个虚拟机。 只能选择可以启用复制的计算机。 选择“确定”。

4. 在 "**设置**" 中，可以配置以下目标站点设置。

    - **目标位置**：复制源虚拟机数据的位置。 Site Recovery 根据所选计算机的位置提供适当目标区域的列表。 建议使用与恢复服务保管库所在位置相同的位置。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Site Recovery 在目标区域中创建新的资源组。 名称获取 "asr" 后缀。 如果 Azure Site Recovery 创建的资源组已存在，则会重复使用该资源组。 你还可以选择对其进行自定义，如下一节所示。 目标资源组的位置可以是托管源虚拟机所在区域之外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 在目标区域中创建新的虚拟网络。 名称获取 "asr" 后缀。 它将映射到源网络，并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户（如果源 vm 不使用托管磁盘）** ：默认情况下，Site Recovery 通过模拟源 vm 存储配置来创建新的目标存储帐户。 如果存储帐户已存在，则会重复使用它。
    - **副本托管磁盘（如果源 vm 使用托管磁盘）** ： Site Recovery 在目标区域中创建新的副本托管磁盘，以将相同存储类型（标准或高级）的源 vm 的托管磁盘与源 vm 的托管磁盘进行镜像。
    - **缓存存储帐户**： Site Recovery 需要在源区域中称为 "*缓存存储*" 的额外存储帐户。 源 Vm 上的所有更改都将被跟踪并发送到缓存存储帐户。 然后，将它们复制到目标位置。
    - **可用性集**：默认情况下，Site Recovery 在目标区域中创建新的可用性集。 名称具有 "asr" 后缀。 如果 Site Recovery 创建的可用性集已存在，则会重复使用。
    - **磁盘加密密钥保管库**：默认情况下，Site Recovery 在目标区域中创建新的密钥保管库。 它有一个基于源 VM 磁盘加密密钥的 "asr" 后缀。 如果 Azure Site Recovery 创建的密钥保管库已存在，则会重复使用。
    - **密钥加密密钥保管库**：默认情况下，Site Recovery 在目标区域中创建新的密钥保管库。 该名称有一个基于源 VM 密钥加密密钥的 "asr" 后缀。 如果 Azure Site Recovery 创建的密钥保管库已存在，则会重复使用它。
    - **复制策略**：定义恢复点保留历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 会创建一个新的复制策略，其中默认设置为*24 小时*（对于应用一致性快照频率为24小时 *）和60分钟*。

## <a name="customize-target-resources"></a>自定义目标资源

按照以下步骤修改 Site Recovery 默认目标设置。

1. 选择 "目标订阅" 旁边的 "**自定义**" 来修改默认的目标订阅。 从 Azure AD 租户中可用的订阅列表中选择订阅。

2. 选择 "资源组、网络、存储和可用性集" 旁边的 "**自定义**"，以修改以下默认设置：
    - 对于 "**目标资源组**"，从订阅目标位置中的资源组列表中选择资源组。
    - 对于 "**目标虚拟网络**"，请从目标位置中的虚拟网络列表中选择网络。
    - 对于**可用性集**，如果虚拟机属于源区域中的可用性集，则可以向其添加可用性集设置。
    - 对于**目标存储帐户**，请选择要使用的帐户。

2. 选择 "加密设置" 旁边的 "**自定义**" 来修改以下默认设置：
   - 对于**目标磁盘加密密钥保管库**，请从订阅目标位置中的密钥保管库列表中选择目标磁盘加密密钥保管库。
   - 对于 "**目标密钥加密密钥保管库**"，请从订阅目标位置中的密钥保管库列表中选择目标密钥加密密钥保管库。

3. 选择 "**创建目标资源**" > "**启用复制**"。
4. 为 Vm 启用复制后，可以在 "**复制的项**" 下查看 vm 的运行状况状态。

>[!NOTE]
>在初始复制期间，状态可能需要一些时间来刷新，而不会有明显的进度。 单击 "**刷新**" 以获取最新状态。

## <a name="update-target-vm-encryption-settings"></a>更新目标 VM 加密设置
在以下情况下，你将需要更新目标 VM 加密设置：
  - 已在 VM 上启用 Site Recovery 复制。 稍后，在源 VM 上启用磁盘加密。
  - 已在 VM 上启用 Site Recovery 复制。 稍后，你更改了源 VM 上的磁盘加密密钥或密钥加密密钥。

你可以使用[脚本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)将加密密钥复制到目标区域，然后在**恢复服务保管库**中更新目标加密设置 *， >  > * **属性** > **计算和网络**"。

!["更新 ADE 设置" 对话框窗口](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Azure 到 Azure VM 复制过程中的密钥保管库权限问题疑难解答

Azure Site Recovery 要求至少具有对源区域密钥保管库的读取权限和对目标区域密钥保管库的写入权限，才能读取机密并将其复制到目标区域密钥保管库。 

**原因1：** 你没有**源区域密钥保管库**的 "GET" 权限来读取密钥。 </br>
**如何修复：** 无论你是否是订阅管理员，都必须拥有对密钥保管库的访问权限，这一点很重要。

1. 请访问源区域密钥保管库，此示例中为 "ContososourceKeyvault" >**访问策略** 
2. 在 "**选择主体**添加用户名" 下，例如： "dradmin@contoso.com"
3. 在 "**密钥权限**" 下选择 "获取" 
4. 在 "**机密权限**" 下，选择 GET 
5. 保存访问策略

**原因2：** 你没有对**目标区域密钥保管库**所需的权限来写入密钥。 </br>

*例如*：尝试复制在源区域上具有 Key vault *ContososourceKeyvault*的 VM。
你拥有源区域密钥保管库的所有权限。 但在保护期间，选择已创建的、不具有权限的密钥保管库 ContosotargetKeyvault。 出现错误。

[目标密钥保管库](#required-user-permissions)所需的权限

**如何修复：** 请参阅**Home** > **Keyvaults** > **ContosotargetKeyvault** > **访问策略**并添加适当的权限。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
