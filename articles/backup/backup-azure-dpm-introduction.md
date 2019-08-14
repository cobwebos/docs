---
title: 准备 DPM 服务器将工作负载备份到 Azure
description: 介绍了如何将 DPM 数据备份到 Azure 恢复服务保管库。
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 71070a778e54e51cdb528041f746489bb64e979c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954713"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>使用 System Center DPM 准备将工作负载备份到 Azure

本文介绍如何使用 Azure 备份服务准备将 System Center Data Protection Manager (DPM) 备份到 Azure。

本文将提供：

- 使用 Azure 备份部署 DPM 的概述。
- 将 Azure 备份与 DPM 结合使用的先决条件和限制。
- 准备 Azure 的步骤，包括设置恢复服务备份保管库，以及视需要修改保管库的 Azure 存储类型。
- 准备 DPM 服务器的步骤，包括下载保管库凭据，安装 Azure 备份代理，以及在保管库中注册 DPM 服务器。
- 常见错误的故障排除提示。


## <a name="why-back-up-dpm-to-azure"></a>为什么将 DPM 备份到 Azure？

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) 备份文件和应用程序数据。 DPM 可与 Azure 备份交互，如下所述：

* 物理服务器或本地 VM 上运行的 DPM — 除了磁盘和磁带备份以外，还可以在 Azure 中将数据备份到备份保管库。
* Azure VM 上运行的 DPM — 从 System Center 2012 R2 Update 3 或更高版本起，可以在 Azure VM 上部署 DPM。 可以将数据备份到附加到 VM 的 Azure 磁盘，或使用 Azure 备份将数据备份到备份保管库。

将 DPM 服务器备份到 Azure 所带来的业务好处包括：

* 若是本地 DPM，Azure 备份可以取代长期的磁带部署。
* 若是 Azure VM 上运行的 DPM，Azure 备份允许你卸载 Azure 磁盘中的存储。 通过将较旧的数据存储在备份保管库中，可以将新数据存储到磁盘，从而纵向扩展业务。

## <a name="prerequisites-and-limitations"></a>先决条件和限制

**设置** | 要求
--- | ---
Azure VM 上的 DPM | System Center 2012 R2 DPM 2012 R2 更新汇总 3 或更高版本。
物理服务器上的 DPM | System Center 2012 SP1 或更高版本；System Center 2012 R2。
Hyper-V VM 上的 DPM | System Center 2012 SP1 或更高版本；System Center 2012 R2。
VMware VM 上的 DPM | System Center 2012 R2 更新汇总 5 或更高版本。
组件 | DPM 服务器应已安装 Windows PowerShell 和 .NET Framework 4.5。
支持的应用 | [了解](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) DPM 可以进行哪些备份。
支持的文件类型 | 使用 Azure 备份，可以备份下列文件类型：加密（仅完整备份）；压缩（支持增量备份）；稀疏（支持增量备份）；压缩和稀疏（处理为稀疏）。
不受支持的文件类型 | 区分大小写的文件系统上的服务器；硬链接（跳过）；重分析点（跳过）；加密和压缩（跳过）；加密和稀疏（跳过）；压缩流；分析流。
本地存储 | 要备份的每台计算机上的可用本地存储必须至少为要备份的数据大小的 5%。 例如，如果要备份 100 GB 的数据，则暂存位置至少需要 5 GB 的可用空间。
保管库存储 | 可以备份到 Azure 备份保管库的数据量没有限制，但数据源（例如，虚拟机或数据库）的大小不应超过 54400 GB。
Azure ExpressRoute | 如果 Azure ExpressRoute 配置了专用或 Microsoft 对等互连，则不能使用它将数据备份到 Azure。<br/><br/> 如果 Azure ExpressRoute 配置了公共对等互连，则可以使用它将数据备份到 Azure。<br/><br/> **注意：** 对于新线路，公共对等互连已弃用。
Azure 备份代理 | 如果 DPM 正在 System Center 2012 SP1 上运行，请安装 DPM SP1 汇总 2 或更高版本。 这是代理安装所必需的。<br/><br/> 本文介绍如何部署最新版本的 Azure 备份代理（也称为 Microsoft Azure 恢复服务 (MARS) 代理）。 如果已部署早期版本，请更新到最新版本以确保备份按预期运行。

在开始之前，需要一个启用了 Azure 备份功能的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 阅读[Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)的相关信息。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>修改存储设置

可以在异地冗余存储与本地冗余存储之间进行选择。

- 默认情况下，保管库具有异地冗余存储。
- 如果保管库是主要备份，请将选项保持设置为异地冗余存储。 如果想要一个更便宜、但持久性不太高的选项，请使用以下过程配置本地冗余存储。
- 了解 [Azure 存储](../storage/common/storage-redundancy.md)，以及[异地冗余](../storage/common/storage-redundancy-grs.md)和[本地冗余](../storage/common/storage-redundancy-lrs.md)存储选项。
- 在初始备份之前修改存储设置。 如果已备份某个项，请先停止在保管库备份该项，再修改存储设置。

若要编辑存储复制设置，请执行以下操作：

1. 打开保管库仪表板。

2. 在“管理”中，单击“备份基础结构”。

3. 在“备份配置”菜单中，为保管库选择存储选项。

    ![备份保管库列表](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>下载保管库凭据

在保管库中注册 DPM 服务器时使用保管库凭据。

- 保管库凭据文件是门户为每个备份保管库生成的证书。
- 然后，门户会将公钥上传到访问控制服务 (ACS)。
- 在执行计算机注册工作流期间，证书的私钥将可供用户使用，它用来对计算机进行身份验证。
- Azure 备份服务根据身份验证将数据发送到所标识的保管库。

### <a name="best-practices-for-vault-credentials"></a>保管库凭据的最佳做法

若要获取这些凭据, 请从 Azure 门户的安全通道下载保管库凭据文件:

- 保管库凭据仅在注册工作流的过程中使用。
- 你需负责确保保管库凭据文件安全且不会泄露。
    - 如果失去了对凭据的控制权，则保管库凭据可能会被用来向保管库注册其他计算机。
    - 但是，备份数据是使用属于客户的通行短语加密的，因此现有的备份数据不会泄露。
- 确保将文件保存在可从 DPM 服务器访问的位置。 如果将它存储在文件共享/SMB 中，请检查访问权限。
- 保管库凭据会在 48 小时后过期。 可以根据需要任意下载新的保管库凭据。 不过，在注册工作流中只能使用最新的保管库凭据文件。
- Azure 备份服务不知道证书的私钥，并且私钥在门户或服务中不可用。

按如下方式将保管库凭据文件下载到本地计算机：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 打开要在其中注册 DPM 服务器的保管库。
3. 在“设置”中，单击“属性”。

    ![打开保管库菜单](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. 在“属性” > “备份凭据”中，单击“下载”。 该门户使用保管库名称和当前日期的组合生成保管库凭据文件，并使其可供下载。

    ![下载](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. 单击“保存”以将保管库凭据下载到文件夹，或单击“另存为”并指定位置。 生成文件最长需要一分钟时间。


## <a name="install-the-backup-agent"></a>安装备份代理

通过 Azure 备份来备份的每台计算机必须安装备份代理（也称为 Microsoft Azure 恢复服务 [MARS] 代理）。 按如下方式在 DPM 服务器上安装代理：

1. 打开要在其中注册 DPM 服务器的保管库。
2. 在“设置”中，单击“属性”。

    ![打开保管库菜单](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. 在“属性”页上，下载 Azure 备份代理。

    ![下载](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. 下载后，运行 MARSAgentInstaller.exe。 以在 DPM 计算机上安装代理。
5. 为代理选择安装文件夹和缓存文件夹。 缓存位置的可用空间必须至少为备份数据的 5%。
6. 如果使用代理服务器连接到 Internet，请在“代理配置”屏幕中，输入代理服务器详细信息。 如果使用已经过身份验证的代理，请在此屏幕中输入用户名和密码详细信息。
7. Azure 备份代理将安装 .NET Framework 4.5 和 Windows PowerShell（如果未安装）以完成安装。
8. 安装代理后，关闭该窗口。

    ![关闭](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>在保管库中注册 DPM 服务器

1. 在 DPM 管理员控制台 >“管理”中，单击“联机”。 选择“注册”。 此时会打开注册服务器向导。
2. 在“代理配置”中，根据需要指定代理设置。

    ![代理配置](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. 在“备份保管库”中，浏览到已下载的保管库凭据文件并选择该文件。

    ![保管库凭据](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. 在“限制设置”中，可以选择性地为备份启用带宽限制。 可以为指定的工作小时和天数设置速度限制。

    ![限制设置](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. 在“恢复文件夹设置”中，指定可在数据恢复期间使用的位置。

    - Azure 备份将此位置用作已恢复数据的临时保存区域。
    - 完成数据恢复后，Azure 备份将清除此区域中的数据。
    - 该位置的空间必须足以容纳你预计并行恢复的项。

    ![恢复文件夹设置](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. 在“加密设置”中，生成或提供通行短语。

    - 通行短语用于加密向云中进行的备份。
    - 至少指定 16 个字符.
    - 将文件保存在安全的位置以便进行恢复。

    ![加密](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > 加密通行短语由你拥有，Microsoft 看不到该通行短语。
    > 如果丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份的数据。

13. 单击“注册”以向保管库注册 DPM 服务器。

服务器成功注册到保管库后，现在，可以开始备份到 Microsoft Azure。

## <a name="troubleshoot-vault-credentials"></a>对保管库凭据进行故障排除

### <a name="expiration-error"></a>过期错误

保管库凭据文件只能生效 48 小时（从门户下载后算起）。 如果此屏幕中显示任何错误（例如“提供的保管库凭据文件已过期”），请登录到 Azure 门户，并再次下载保管库凭据文件。

### <a name="access-error"></a>访问错误

确保保管库凭据文件在安装应用程序可访问的位置中可用。 如果遇到访问相关的错误，请将保管库凭据文件复制到此计算机中的临时位置，并重试操作。

### <a name="invalid-credentials-error"></a>凭据无效错误

如果遇到无效的保管库凭据错误（例如“所提供的保管库凭据无效”），则该文件已损坏，或者没有与恢复服务关联的最新凭据。

- 请在从门户下载新的保管库凭据文件后重试该操作。
- 在 Azure 门户中快速连续单击两次“下载保管库凭据”选项时，通常会出现此错误。 在这种情况下，只有第二个保管库凭据文件有效。
