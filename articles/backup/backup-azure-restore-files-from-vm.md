---
title: 从 Azure VM 备份恢复文件和文件夹
description: 本文介绍如何从 Azure 虚拟机恢复点恢复文件和文件夹。
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273301"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>从 Azure 虚拟机备份恢复文件

Azure 备份提供从 Azure VM 备份（也称恢复点）还原 [Azure 虚拟机 (VM) 和磁盘](./backup-azure-arm-restore-vms.md)的功能。 本文介绍如何从 Azure VM 备份恢复文件和文件夹。 还原文件和文件夹仅适用于使用资源管理器模型部署的、在恢复服务保管库中受保护的 Azure VM。

> [!NOTE]
> 此功能适用于使用 Resource Manager 模型部署的、在恢复服务保管库中受保护的 Azure VM。
> 不支持从加密的 VM 备份恢复文件。
>

## <a name="mount-the-volume-and-copy-files"></a>装载卷并复制文件

若要从恢复点还原文件或文件夹，请转到虚拟机并选择所需的恢复点。

1. 登录到 [Azure 门户](https://portal.Azure.com)，在左侧窗格中单击“虚拟机”****。 从虚拟机列表中，选择虚拟机以打开其仪表板。

2. 在虚拟机菜单中，单击“备份”**** 以打开“备份”仪表板。

    ![打开恢复服务保管库备份项](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. 在“备份”仪表板菜单上，单击“文件恢复”****。

    ![“文件恢复”按钮](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    此时将打开“文件恢复”**** 菜单。

    ![文件恢复菜单](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. 从“选择恢复点”下拉菜单中，选择包含所需文件的恢复点。**** 默认已选择最新的恢复点。

5. 要从恢复点下载用于复制文件的软件，请单击 **"下载可执行文件**"（适用于 Windows Azure VM）或 **"下载脚本**"（对于 Linux Azure VM，将生成 python 脚本）。

    ![生成的密码](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure 会将该可执行文件或脚本下载到本地计算机。

    ![可执行文件或脚本的下载消息](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    要以管理员身份运行可执行文件或脚本，建议您将下载的文件保存到计算机。

6. 该可执行文件或脚本受密码保护，需要密码才能运行。 在“文件恢复”**** 菜单上，单击复制按钮以将密码加载到内存中。

    ![生成的密码](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 从下载位置（通常是“下载”文件夹），右键单击该可执行文件或脚本，然后用管理员凭据运行。 出现提示时，键入密码或从内存中粘贴密码，然后按**Enter**。 输入有效的密码后，脚本将连接到恢复点。

    ![文件恢复菜单](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. 对于 Linux 计算机，将生成 python 脚本。 需要下载脚本并将其复制到相关/兼容的 Linux 服务器。 您可能需要修改权限才能使用```chmod +x <python file name>```执行它。 然后使用```./<python file name>```运行 python 文件。

请参阅[访问要求](#access-requirements)部分以确保脚本成功运行。

### <a name="identifying-volumes"></a>标识卷

#### <a name="for-windows"></a>对于 Windows

运行可执行文件时，操作系统将装载新卷并分配驱动器号。 可以使用 Windows 资源管理器或文件资源管理器来浏览这些驱动器。 分配给卷的驱动器号可能与原始虚拟机不同。 但是，将保留卷名称。 例如，如果原始虚拟机上的卷是"数据磁盘 （E：`\`）"，则该卷可以附加在本地计算机上为"数据磁盘（"任何字母"）：`\` 浏览脚本输出中提及的所有卷，直到找到文件或文件夹。  

   ![文件恢复菜单](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>对于 Linux

在 Linux 中，恢复点的卷将装载到运行脚本的文件夹。 将相应地显示附加的磁盘、卷和对应装载路径。 这些装载路径对于具有根级别访问权限的用户可见。 浏览脚本输出中涉及的卷。

  ![Linux 文件恢复菜单](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>关闭连接

识别文件并将其复制到本地存储位置后，请删除（或卸载）其他驱动器。 若要卸载驱动器，请在 Azure 门户中的“文件恢复”菜单上，单击“卸载磁盘”。********

![卸载磁盘](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

卸载磁盘后，会收到一条消息。 连接可能在几分钟时间后才会刷新，以便能够删除磁盘。

在 Linux 中，断开与恢复点的连接后，OS 不会自动删除相应装载路径。 装载路径作为"孤立"卷存在，并且可见，但在访问/写入文件时会引发错误。 这些卷可以手动删除。 该脚本运行时会标识以前的任何恢复点存在的任何此类卷，并在获得许可后将其清除。

## <a name="special-configurations"></a>特殊配置

### <a name="dynamic-disks"></a>动态磁盘

如果受保护的 Azure VM 包含带有以下一个或两个特征的卷，则无法在同一 VM 上运行该可执行脚本。

- 跨多个磁盘的卷（跨区卷和带区卷）
- 动态磁盘上的容错卷（镜像卷和 RAID-5 卷）

而应在具有兼容操作系统的任何其他计算机上运行该可执行脚本。

### <a name="windows-storage-spaces"></a>Windows 存储空间

Windows 存储空间是用于将存储器虚拟化的一种 Windows 技术。 使用 Windows 存储空间，可将行业标准磁盘分组为存储池。 然后使用这些存储池中的可用空间创建虚拟磁盘，即存储空间。

如果受保护的 Azure VM 使用 Windows 存储空间，则不能在同一 VM 上运行该可执行脚本。 而应在具有兼容操作系统的任何其他计算机上运行该可执行脚本。

### <a name="lvmraid-arrays"></a>LVM/RAID 阵列

在 Linux 中，逻辑卷管理器 (LVM) 和/或软件 RAID 阵列用于管理多个磁盘上的逻辑卷。 如果受保护的 Linux VM 使用 LVM 和/或 RAID 阵列，则不能在同一 VM 上运行该脚本。 而应在具有兼容 OS 且支持受保护 VM 的文件系统的任何其他计算机上运行该脚本。

以下脚本输出显示了 LVM 和/或 RAID 阵列磁盘和卷，及其分区类型。

   ![Linux LVM 输出菜单](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

若要使这些分区联机，请运行以下各部分中的命令。

#### <a name="for-lvm-partitions"></a>对于 LVM 分区

要在物理卷下列出卷组名称，可以：

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

要在卷组中列出所有逻辑卷、名称及其路径，

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

要将逻辑卷装载到您选择的路径：

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>对于 RAID 阵列

以下命令显示有关所有突袭磁盘的详细信息：

```bash
#!/bin/bash
mdadm –detail –scan
```

 相关 RAID 磁盘显示为 `/dev/mdm/<RAID array name in the protected VM>`

如果 RAID 磁盘具有物理卷，请使用装载命令：

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

如果 RAID 磁盘配置了另一个 LVM，则对 LVM 分区使用上述过程，但使用卷名称代替 RAID 磁盘名称。

## <a name="system-requirements"></a>系统要求

### <a name="for-windows-os"></a>对于 Windows OS

下表显示了服务器与计算机操作系统之间的兼容性。 恢复文件时，不能将文件还原到更旧或更新的操作系统版本。 例如，不能将文件从 Windows Server 2016 VM 还原到 Windows Server 2012 或 Windows 8 计算机。 可将 VM 中的文件还原到相同的服务器操作系统，或还原到兼容的客户端操作系统。

|服务器 OS | 兼容的客户端 OS  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>对于 Linux OS

在 Linux 中，用于还原文件的计算机的 OS 必须支持受保护虚拟机的文件系统。 选择用于运行脚本的计算机时，请确保计算机具有兼容的 OS，并使用下表中认定的版本之一：

|Linux OS | 版本  |
| --------------- | ---- |
| Ubuntu | 12.04 及更高版本 |
| CentOS | 6.5 及更高版本  |
| RHEL | 6.7 及更高版本 |
| Debian | 7 及更高版本 |
| Oracle Linux | 6.4 及更高版本 |
| SLES | 12 及更高版本 |
| OpenSUSE | 42.2 及更高版本 |

> [!NOTE]
> 我们发现在具有 SLES 12 SP4 操作系统的计算机上运行文件恢复脚本时存在一些问题，我们正在与 SLES 团队一起进行调查。
> 目前，可以在装有 SLES 12 SP2 和 SP3 OS 版本的计算机上运行文件恢复脚本。
>

该脚本还需要 Python 和 bash 组件才能执行并安全地连接到恢复点。

|组件 | 版本  |
| --------------- | ---- |
| bash | 4 及更高版本 |
| Python | 2.6.6 及更高版本  |
| TLS | 应支持 1.2  |

## <a name="access-requirements"></a>访问要求

如果在访问受限的计算机上运行该脚本，请确保能够访问：

- `download.microsoft.com`
- 恢复服务 URL（地区名称是指恢复服务保管库的区域）
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`（适用于 Azure 公共地区）
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`（适用于 Azure 中国世纪互联）
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`（适用于 Azure 美国政府）
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`（适用于 Azure 德国）
- 出站端口 53 （DNS）， 443， 3260

> [!NOTE]
>
> - 下载的脚本文件名将在 URL 中填写**地理名**。 对于\'考试：下载的脚本名称以 VMname\'\_\'地理名称\'+\'GUID\'开头，如*ContosoVM_wcus_12345678*
> - URL 将是<https://pod01-rec2.wcus.backup.windowsazure.com>"
>

在 Linux 上，该脚本需要“open-iscsi”和“lshw”组件才能连接到恢复点。 如果运行脚本的计算机上不存在组件，则脚本请求安装组件的权限。 请同意安装必需组件。

需要访问以`download.microsoft.com`下载用于在运行脚本的计算机和恢复点中的数据之间建立安全通道的组件。

可以在具有与备份 VM 相同（或兼容）操作系统的任何计算机上运行该脚本。 有关兼容的操作系统，请参阅[兼容的 OS 表](backup-azure-restore-files-from-vm.md#system-requirements)。 如果受保护的 Azure 虚拟机使用 Windows 存储空间（适用于 Windows Azure VM）或 LVM/RAID 阵列（适用于 Linux VM），则不能在同一虚拟机上运行该可执行文件或脚本。 而应在具有兼容操作系统的任何其他计算机上运行该可执行文件或脚本。

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>具有大型磁盘的虚拟机备份的文件恢复

本节介绍如何从具有 16 个以上磁盘的 Azure 虚拟机的备份中执行文件恢复，并且每个磁盘大小大于 32 TB。

由于文件恢复过程从备份中附加所有磁盘，因此当使用大量磁盘（>16）或大型磁盘（每个磁盘> 32 TB）时，建议执行以下操作点：

- 保留单独的还原服务器（Azure VM D2v3 VM）以进行文件恢复。 只能将其用于文件恢复，然后在不需要时将其关闭。 不建议在原始计算机上还原，因为它将对 VM 本身产生重大影响。
- 然后运行脚本一次，检查文件恢复操作是否成功。
- 如果文件恢复过程挂起（磁盘从未装载或已装载，但卷未显示），则执行以下步骤。
  - 如果还原服务器是 Windows VM：
    - 确保操作系统为 WS 2012 或更高版本。
    - 确保注册表项在还原服务器中按以下建议设置，并确保重新启动服务器。 GUID 旁边的数字范围为 0001-0005。 在下面的示例中，它是 0004。 导航注册表键路径，直到参数部分。

    ![iscsi-reg-key 更改. png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 如果还原服务器是 Linux VM：
  - 在文件 /etc/iscsi/iscsi/iscsid.conf 中，从以下更改设置：
    - 节点.conn_0_timeo.noop_out_timeout = 5 到节点.conn_0_timeo.noop_out_timeout = 30
- 进行上述更改后，再次运行脚本。 通过这些更改，文件恢复很有可能成功。
- 每次用户下载脚本时，Azure 备份都会启动准备下载恢复点的过程。 使用大型磁盘，此过程将需要相当长的时间。 如果连续出现突发请求，目标准备将进入下载螺旋。 因此，建议从门户/Powershell/CLI 下载脚本，等待 20-30 分钟（启发式），然后运行它。 此时，目标应准备好从脚本进行连接。
- 文件恢复后，请确保返回门户，然后单击 **"卸载磁盘**"，查看无法装载卷的恢复点。 从本质上讲，此步骤将清除任何现有进程/会话，并增加恢复的机会。

## <a name="troubleshooting"></a>疑难解答

如果从虚拟机恢复文件时遇到问题，请查看下表了解更多信息。

| 错误消息/情景 | 可能的原因 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| Exe 输出：*连接到目标时捕获异常* | 脚本无法访问恢复点    | 检查计算机是否满足[以前的访问要求](#access-requirements)。 |  
| Exe 输出：*目标已通过 iSCSI 会话登录。* | 脚本已在同一台计算机上执行，并且已附加驱动器 | 已附加恢复点所在的卷。 不能使用与原始 VM 相同的驱动器号装载这些卷。 浏览文件资源管理器中的所有可用卷。 |
| Exe 输出：*此脚本无效，因为磁盘已通过门户卸载/超过 12 小时限制。从门户下载新脚本。* |    磁盘已从门户卸载或超过 12 小时限制 | 此特定可执行文件现已失效，无法运行。 如果要访问该恢复点的文件，请访问门户获取新的 exe。|
| 在运行 exe 的计算机上：单击卸载按钮后，新卷不会卸载 | 计算机上的 iSCSI 创建器未响应/刷新其与目标的连接并维护缓存。 |  单击“卸除”**** 后，请等待几分钟。 如果未卸载新卷，请浏览所有卷。 浏览所有卷会强制发起器刷新连接，并且卷将卸载，并显示磁盘不可用的错误消息。|
| Exe 输出：脚本成功运行，但脚本输出上未显示"附加的新卷" |    这是暂时性的错误    | 卷已附加。 打开资源管理器即可浏览它们。 如果每次都使用同一台计算机运行脚本，请考虑重新启动计算机，并且列表应在后续的 exe 运行中显示。 |
| Linux 特定：无法查看所需的卷 | 运行脚本的计算机的 OS 可能无法识别受保护 VM 的基础文件系统 | 检查恢复点是崩溃一致性还是文件一致性。 如果文件一致，则在另一台计算机上运行脚本，其操作系统可识别受保护的 VM 文件系统。 |
| Windows 特定：无法查看所需的卷 | 磁盘可能已连接，但未配置卷 | 从磁盘管理屏幕中，识别与恢复点相关的其他磁盘。 如果这些磁盘中的任何一个处于脱机状态，请尝试通过右键单击磁盘将其联机，然后单击"**联机**"。|

## <a name="security"></a>安全性

本节讨论为实现 Azure VM 备份的文件恢复而采取的各种安全措施。

### <a name="feature-flow"></a>功能流

此功能旨在访问 VM 数据，而无需还原整个 VM 或 VM 磁盘，并且步骤数最少。 对 VM 数据的访问由脚本提供（脚本在运行时装载恢复卷，如下所示），它是所有安全实现的基石：

  ![安全功能流](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>安全实现

#### <a name="select-recovery-point-who-can-generate-script"></a>选择恢复点（谁可以生成脚本）

该脚本提供对 VM 数据的访问，因此首先规范谁可以生成 VM 数据非常重要。 您需要登录到 Azure 门户，并[授权 RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)生成脚本。

文件恢复所需的授权级别与 VM 还原和磁盘还原所需的授权级别相同。 换句话说，只有那些有权查看 VM 数据的用户可以生成此脚本。

生成的脚本使用 Azure 备份服务的官方 Microsoft 证书进行签名。 对脚本的任何篡改都意味着签名被损坏，操作系统将突出显示任何运行脚本的尝试都是一个潜在风险。

#### <a name="mount-recovery-volume-who-can-run-script"></a>装载恢复卷（谁可以运行脚本）

只有管理员才能运行该脚本，并且它应该在提升模式下运行。 脚本仅运行一组预先生成的步骤，不接受来自任何外部源的输入。

要运行脚本，需要一个密码，该密码仅在在 Azure 门户或 PowerShell/CLI 生成脚本时向授权用户显示。 这是为了确保下载脚本的经授权的用户也负责运行脚本。

#### <a name="browse-files-and-folders"></a>浏览文件和文件夹

若要浏览文件和文件夹，需通过此脚本使用计算机中的 iSCSI 发起程序连接到已配置为 iSCSI 目标的恢复点。 在这里，你可以想象一个人试图模仿/欺骗任一/所有组件的情况。

我们使用相互 CHAP 身份验证机制，以便每个组件对另一个组件进行身份验证。 这意味着，假开始器很难连接到 iSCSI 目标，并且假目标连接到运行脚本的计算机。

通过通过 TCP 构建安全的 TLS 隧道（运行脚本的计算机[应支持 TLS 1.2），](#system-requirements)保护恢复服务和计算机之间的数据流。

父/备份 VM 中存在的任何文件访问控制列表 （ACL） 也保存在装载的文件系统中。

此脚本允许以只读方式访问恢复点，其有效时间仅为 12 小时。 如果要提前删除访问权限，请登录到 Azure 门户/PowerShell/CLI，并针对该特定恢复点执行**卸载磁盘**。 该脚本会立即失效。

## <a name="next-steps"></a>后续步骤

- 有关还原文件时的任何问题，请参阅[故障排除](#troubleshooting)部分
- 了解如何通过[Powershell 还原文件](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- 了解如何通过[Azure CLI 还原文件](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- 还原 VM 后，了解如何[管理备份](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
