---
title: 安装 Microsoft Azure 恢复服务 (MARS) 代理
description: 了解如何安装 Microsoft Azure 恢复服务 (MARS) 代理来备份 Windows 计算机。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: fb59c245c469791233ce973b00426a127b116535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975309"
---
# <a name="install-the-azure-backup-mars-agent"></a>安装 Azure 备份 MARS 代理

本文介绍如何将 Microsoft Azure 恢复服务 (MARS) 代理安装。 MARS 也称为 Azure 备份代理。

## <a name="about-the-mars-agent"></a>关于 MARS 代理

Azure 备份使用 MARS 代理备份本地计算机和 Azure VM 中的文件、文件夹和系统状态。 这些备份存储在 Azure 中的恢复服务保管库中。 可在以下位置运行代理：

* 直接在本地 Windows 计算机上。 这些计算机可直接备份到 Azure 中的备份恢复服务保管库。
* 在运行 Windows（与 Azure VM 备份扩展一起运行）的 Azure VM 上。 代理将备份 VM 上的特定文件和文件夹。
* 在 Microsoft Azure 备份 Server (MABS) 实例或 System Center Data Protection Manager (DPM) Server。 在此方案中，计算机和工作负荷将备份到 MABS 或 Data Protection Manager。 然后 MABS 或 Data Protection Manager 将通过 MARS 代理备份到 Azure 中的保管库。

可备份的数据取决于代理的安装位置。

> [!NOTE]
> 通常我们会使用 VM 上的 Azure 备份扩展来备份 Azure VM。 此方法将备份整个 VM。 若要备份 VM 上的特定文件和文件夹，请安装 MARS 代理并将其与该扩展一起使用。 有关详细信息，请参阅[内置 Azure VM 备份的体系结构](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![备份过程的步骤](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>开始之前

* 了解 [Azure 备份如何使用 MARS 代理备份 Windows 计算机](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 了解在辅助 MABS 或 Data Protection Manager 服务器上运行 MARS 代理的[备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看 MARS 代理[支持的操作以及可备份的内容](backup-support-matrix-mars-agent.md)。
* 如果需要将服务器或客户端备份到 Azure，请确保有一个 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 验证要备份的计算机的 Internet 访问权限。
* 确保执行 MARS 代理的安装和配置的用户在要保护的服务器上具有本地管理员特权。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修复存储复制

默认情况下，保管库使用[异地冗余存储 (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)。

* 如果保管库是主要备份机制，建议使用 GRS。
* 可以使用[本地冗余存储 (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) 来降低 Azure 存储成本。

若要修改存储复制类型，请执行以下操作：

1. 在新保管库的“设置”部分选择“属性”。

1. 在“属性”页的“备份配置”下选择“更新”。  

1. 选择存储复制类型，然后选择“保存”。

    ![更新备份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 在保管库经过设置并且包含备份项之后，无法修改存储复制类型。 若要修改，需要重新创建保管库。
>

### <a name="verify-internet-access"></a>验证 Internet 访问

如果计算机的 Internet 访问状态受限，请确保计算机或代理上的防火墙设置允许以下 URL 和 IP 地址：

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 地址
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>使用 Azure ExpressRoute

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

若要使用公共对等互连，请首先确保能够访问以下域和地址：

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

若要使用 Microsoft 对等互连，请选择以下服务、区域和相关社区值：

* Azure Active Directory (12076:5060)
* Azure 区域（取决于你的恢复服务保管库的位置）
* Azure 存储（取决于你的恢复服务保管库的位置）

有关详细信息，请参阅 [ExpressRoute 路由要求](../expressroute/expressroute-routing.md)。

> [!NOTE]
> 对于新线路，公共对等互连已弃用。

前面的所有 URL 和 IP 地址在端口 443 上使用 HTTPS 协议。

### <a name="private-endpoints"></a>专用终结点

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>下载 MARS 代理

下载 MARS 代理，以便可将其安装到所要备份的计算机上。

如果已在任何计算机上安装该代理，请确保运行该代理的最新版本。 在门户中找到最新版本，或直接转到[下载](https://aka.ms/azurebackup_agent)。

1. 在保管库中的“开始使用”下，选择“备份”。 

    ![打开备份代理](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. 在“工作负荷的运行位置?”下，选择“本地”。  即使你要在 Azure VM 上安装 MARS 代理，也应选择此选项。
1. 在“要备份哪些内容?”下，选择“文件和文件夹”。  还可以选择“系统状态”。 还有其他一些可用选项，但仅当运行的是辅助备份服务器时，这些选项才受支持。 选择“准备基础结构”。

    ![配置文件和文件夹](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. 对于“准备基础结构”，请在“安装恢复服务代理”下，下载 MARS 代理。 

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 在下载菜单中选择“保存”。 默认情况下，*MARSagentinstaller.exe* 文件将保存到 Downloads 文件夹。

1. 选择“已下载或使用最新的恢复服务代理”，然后下载保管库凭据。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. 选择“保存” 。 文件将下载到“下载”文件夹。 无法打开保管库凭据文件。

## <a name="install-and-register-the-agent"></a>安装并注册代理

1. 在要备份的计算机上运行 *MARSagentinstaller.exe* 文件。
1. 在 MARS 代理安装向导中选择“安装设置”。 在此处选择代理的安装位置以及缓存位置。 然后，选择“下一步”。
   * Azure 备份在将数据快照发送到 Azure 之前，会使用缓存来存储这些快照。
   * 缓存位置的可用空间应该至少为所要备份的数据大小的 5%。

    ![在 MARS 代理安装向导中选择安装设置](./media/backup-configure-vault/mars1.png)

1. 对于“代理配置”中，请指定 Windows 计算机上运行的代理如何连接到 Internet。 然后，选择“下一步”。

   * 如果使用自定义代理，请指定任何所需的代理设置和凭据。
   * 请记住，代理需要有权访问[特定的 URL](#before-you-start)。

    ![在 MARS 向导中设置 Internet 访问](./media/backup-configure-vault/mars2.png)

1. 对于“安装”，请复查先决条件，然后选择“安装”。 
1. 安装代理后，选择“继续注册”。
1. 在“注册服务器向导” > “保管库标识”中，浏览并选择已下载的凭据文件。  然后，选择“下一步”。

    ![使用注册服务器向导添加保管库凭据](./media/backup-configure-vault/register1.png)

1. 在“加密设置”页上，指定用于加密和解密计算机备份的通行短语。 有关允许的通行短语字符的详细信息，请参阅[此处](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase)。

    * 将通行短语保存在安全位置。 还原备份时需要用到它。
    * 如果你丢失或忘记了该通行短语，Microsoft 将无法帮助你恢复备份数据。

1. 选择“完成”。 现已安装代理，且已向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 备份 MARS 代理备份 Windows 计算机](backup-windows-with-mars-agent.md)
