---
title: 安装 Microsoft Azure 恢复服务（MARS）代理
description: 了解如何安装 Microsoft Azure 恢复服务（MARS）代理来备份 Windows 计算机。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247756"
---
# <a name="install-the-azure-backup-mars-agent"></a>安装 Azure 备份 MARS 代理

本文介绍如何安装 Microsoft Azure 恢复服务（MARS）代理。 MARS 也称为 Azure 备份代理。

## <a name="about-the-mars-agent"></a>关于 MARS 代理

Azure 备份使用 MARS 代理来备份本地计算机和 Azure Vm 中的文件、文件夹和系统状态。 这些备份存储在 Azure 中的恢复服务保管库中。 您可以运行代理：

* 直接在本地 Windows 计算机上。 这些计算机可以直接备份到 Azure 中的恢复服务保管库。
* 在运行 Windows 的 Azure vm 上，使用 Azure VM 备份扩展。 代理备份 VM 上的特定文件和文件夹。
* 在 Microsoft Azure 备份服务器（MABS）实例或 System Center Data Protection Manager （DPM）服务器上。 在此方案中，计算机和工作负荷备份到 MABS 或 Data Protection Manager。 然后 MABS 或 Data Protection Manager 使用 MARS 代理备份到 Azure 中的保管库。

可用于备份的数据取决于安装代理的位置。

> [!NOTE]
> 通常，可以使用 VM 上的 Azure 备份扩展来备份 Azure VM。 此方法将备份整个 VM。 如果要备份 VM 上的特定文件和文件夹，请在扩展中安装并使用 MARS 代理。 有关详细信息，请参阅[内置 AZURE VM 备份的体系结构](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![备份过程的步骤](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>开始之前

* 了解[Azure 备份如何使用 MARS 代理来备份 Windows 计算机](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 了解在辅助 MABS 或 Data Protection Manager 服务器上运行 MARS 代理的[备份体系结构](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看[支持的内容以及 MARS 代理可以备份](backup-support-matrix-mars-agent.md)的内容。
* 如果需要将服务器或客户端备份到 Azure，请确保具有 Azure 帐户。 如果没有帐户，只需几分钟就能创建一个[免费](https://azure.microsoft.com/free/)帐户。
* 验证要备份的计算机上的 internet 访问权限。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改存储复制

默认情况下，保管库使用[异地冗余存储（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保管库是你的主要备份机制，则建议你使用 GRS。
* 可以使用[本地冗余存储（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)来减少 Azure 存储成本。

若要修改存储复制类型，请执行以下操作：

1. 在新保管库中，选择 "**设置**" 部分下的 "**属性**"。

1. 在 "**属性**" 页上的 "**备份配置**" 下，选择 "**更新**"。

1. 选择存储复制类型，然后选择 "**保存**"。

    ![更新备份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 在设置保管库并包含备份项后，不能修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。
>

### <a name="verify-internet-access"></a>验证 Internet 访问

如果计算机具有有限的 internet 访问权限，请确保计算机或代理上的防火墙设置允许以下 Url 和 IP 地址：

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP 地址
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>使用 Azure ExpressRoute

可以通过 Azure ExpressRoute 使用公共对等互连（适用于旧线路）和 Microsoft 对等互连来备份数据。 不支持通过专用对等互连进行备份。

若要使用公共对等互连，请首先确保访问以下域和地址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

若要使用 Microsoft 对等互连，请选择下列服务、区域和相关团体值：

* Azure Active Directory （12076:5060）
* Azure 区域，具体取决于恢复服务保管库的位置
* Azure 存储空间，根据恢复服务保管库的位置

有关详细信息，请参阅[ExpressRoute 路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

> [!NOTE]
> 新线路不推荐使用公共对等互连。

前面的所有 Url 和 IP 地址使用端口443上的 HTTPS 协议。

### <a name="private-endpoints"></a>专用终结点

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>下载 MARS 代理

下载 MARS 代理，以便可以将其安装在要备份的计算机上。

如果已在任何计算机上安装了代理，请确保运行的是最新版本的代理。 在门户中查找最新版本，或直接跳到[下载](https://aka.ms/azurebackup_agent)。

1. 在保管库中的 "**入门**下，选择"**备份**"。

    ![打开备份目标](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. 在 **"工作负荷的运行位置"** 下，选择 **"本地**"。 即使要在 Azure VM 上安装 MARS 代理，也请选择此选项。
1. 在 "**要备份什么？** " 下，选择 "**文件和文件夹**"。 你还可以选择 "**系统状态**"。 还有很多其他选项可用，但只有在运行辅助备份服务器时才支持这些选项。 选择 "**准备基础结构**"。

    ![配置文件和文件夹](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. 对于 "**准备基础结构**"，在 "**安装恢复服务代理**" 下，下载 MARS 代理。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 在 "下载" 菜单中，选择 "**保存**"。 默认情况下，*MARSagentinstaller.exe* 文件将保存到 Downloads 文件夹。

1. 选择 **"已下载" 或 "使用最新的恢复服务代理**"，然后下载保管库凭据。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. 选择“保存”。 文件将下载到下载文件夹。 不能打开保管库凭据文件。

## <a name="install-and-register-the-agent"></a>安装并注册代理

1. 在要备份的计算机上运行*marsagentinstaller.exe*文件。
1. 在 MARS 代理安装向导中，选择 "**安装设置**"。 在此处，选择要安装代理的位置，并选择缓存的位置。 然后，选择“下一步”。
   * Azure 备份使用缓存来存储数据快照，然后将其发送到 Azure。
   * 缓存位置的可用空间应等于要备份的数据大小的至少5%。

    ![选择 MARS 代理安装向导中的 "安装设置"](./media/backup-configure-vault/mars1.png)

1. 对于 "**代理配置**"，请指定在 Windows 计算机上运行的代理将如何连接到 internet。 然后，选择“下一步”。

   * 如果使用自定义代理，请指定任何所需的代理设置和凭据。
   * 请记住，代理需要访问[特定的 url](#before-you-start)。

    ![在 MARS 向导中设置 internet 访问](./media/backup-configure-vault/mars2.png)

1. 若要**安装**，请查看先决条件，然后选择 "**安装**"。
1. 安装代理后，选择 "**继续注册**"。
1. 在 "**注册服务器" 向导** > **保管库标识**"中，浏览到并选择已下载的凭据文件。 然后，选择“下一步”。

    ![使用 "注册服务器向导" 添加保管库凭据](./media/backup-configure-vault/register1.png)

1. 在 "**加密设置**" 页上，指定将用于对计算机的备份进行加密和解密的通行短语。

    * 将通行短语保存在安全的位置。 你需要它来还原备份。
    * 如果丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。

1. 选择“完成”。 此时将安装代理，并向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 备份 MARS 代理备份 Windows 计算机](backup-windows-with-mars-agent.md)
