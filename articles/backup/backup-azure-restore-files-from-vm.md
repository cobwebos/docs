---
title: 从 Azure VM 备份恢复文件和文件夹
description: 本文介绍如何从 Azure 虚拟机恢复点恢复文件和文件夹。
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: e913fa1e609eff687b5757a566583539b32b1b8e
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757143"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>从 Azure 虚拟机备份恢复文件

Azure 备份提供从 Azure VM 备份（也称恢复点）还原 [Azure 虚拟机 (VM) 和磁盘](./backup-azure-arm-restore-vms.md)的功能。 本文介绍如何从 Azure VM 备份恢复文件和文件夹。 还原文件和文件夹仅适用于使用资源管理器模型部署的 Azure Vm 和保护到恢复服务保管库的 Azure Vm。

> [!NOTE]
> 此功能适用于使用 Resource Manager 模型部署的、在恢复服务保管库中受保护的 Azure VM。
> 不支持从加密的 VM 备份恢复文件。
>

## <a name="mount-the-volume-and-copy-files"></a>装载卷并复制文件

若要从恢复点还原文件或文件夹，请转到虚拟机并选择所需的恢复点。

1. 登录到 [Azure 门户](https://portal.Azure.com) ，并在左窗格中选择 " **虚拟机**"。 从虚拟机列表中，选择虚拟机以打开其仪表板。

2. 在虚拟机的菜单中，选择 " **备份** " 打开 "备份" 仪表板。

    ![打开恢复服务保管库备份项](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. 在备份仪表板菜单中，选择 " **文件恢复**"。

    ![选择文件恢复](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    此时将打开“文件恢复”菜单。

    ![文件恢复菜单](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. 从“选择恢复点”下拉菜单中，选择存储所需文件的恢复点。 默认已选择最新的恢复点。

5. 若要下载用于从恢复点复制文件的软件，请选择 "下载 Windows Azure Vm 的 **可执行** () 或下载适用于 Linux azure Vm 的 **脚本** (，并) 生成 python 脚本。

    ![下载可执行文件](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure 会将该可执行文件或脚本下载到本地计算机。

    ![可执行文件或脚本的下载消息](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    若要以管理员身份运行可执行文件或脚本，建议将下载的文件保存到计算机。

6. 该可执行文件或脚本受密码保护，需要密码才能运行。 在 " **文件恢复** " 菜单中，选择 "复制" 按钮，将密码加载到内存中。

    ![生成的密码](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 请确保[使用符合要求的计算机](#selecting-the-right-machine-to-run-the-script)来执行脚本。 如果符合要求的计算机与下载脚本的计算机相同，则可以继续下载部分的操作。 从下载位置（通常是“下载”文件夹），右键单击可执行文件或脚本，然后用管理员凭据运行。 出现提示时，键入密码或粘贴内存中的密码，然后按 Enter。 输入有效的密码后，脚本将连接到恢复点。

    ![可执行输出](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. 对于 Linux 计算机，将生成一个 Python 脚本。 用户需要下载该脚本并将其复制到相关/兼容的 Linux 服务器。 你可能必须修改权限才能使用 ```chmod +x <python file name>``` 执行该脚本。 然后使用 ```./<python file name>``` 运行 Python 文件。

请参阅[访问要求](#access-requirements)部分来确保脚本已成功运行。

### <a name="identifying-volumes"></a>标识卷

#### <a name="for-windows"></a>对于 Windows

运行可执行文件时，操作系统将装载新卷并分配驱动器号。 可以使用 Windows 资源管理器或文件资源管理器来浏览这些驱动器。 分配给卷的驱动器号不能与原始虚拟机中的驱动器号相同。 不过，卷名会保留。 例如，如果原始虚拟机上的卷为“数据磁盘(E:`\`)”，可在本地计算机上将该卷附加为“数据磁盘(‘任意字母’:`\`)”。 浏览脚本输出中所述的所有卷，直至找到文件或文件夹。  

   ![已附加恢复卷](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>对于 Linux

在 Linux 中，恢复点的卷会装载到运行脚本的文件夹。 将相应地显示附加的磁盘、卷和对应装载路径。 这些装载路径对于具有根级别访问权限的用户可见。 浏览脚本输出中涉及的卷。

  ![Linux 文件恢复菜单](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>关闭连接

识别文件并将其复制到本地存储位置后，请删除（或卸载）其他驱动器。 若要卸载驱动器，请在 Azure 门户中的 " **文件恢复** " 菜单上，选择 " **卸载磁盘**"。

![卸载磁盘](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

卸载磁盘后，会显示一条消息。 连接可能在几分钟时间后才会刷新，以便能够删除磁盘。

在 Linux 中，断开与恢复点的连接后，OS 不会自动删除相应装载路径。 装载路径作为“孤立”的卷存在并且可见，但访问/写入文件时会引发错误。 这些卷可以手动删除。 该脚本运行时会标识以前的任何恢复点存在的任何此类卷，并在获得许可后将其清除。

> [!NOTE]
> 请确保在还原所需文件后关闭连接。 这一点非常重要，尤其是在执行脚本的计算机的情况下，也为备份配置了。 如果连接仍处于打开状态，则后续的备份可能会失败并出现错误 "UserErrorUnableToOpenMount"。 出现这种情况的原因是，装载的驱动器/卷被假定为可用，并且在被访问时可能会失败，因为基础存储（即 iSCSI 目标服务器）可能不可用。 清理连接将删除这些驱动器/卷，因此它们将在备份期间不可用。

## <a name="selecting-the-right-machine-to-run-the-script"></a>选择符合要求的计算机来运行脚本

若已成功下载脚本，下一步就是验证计划在其上执行脚本的计算机是否为符合要求的计算机。 以下是该计算机需要满足的要求。

### <a name="original-backed-up-machine-versus-another-machine"></a>原始备份计算机与其他计算机

1. 如果备份的计算机是大磁盘 VM（即磁盘数量大于 16 个，或者每个磁盘的大小大于 4 TB），则必须在另一台计算机上执行脚本，并且该计算机需要满足[这些要求](#file-recovery-from-virtual-machine-backups-having-large-disks)。
1. 即使备份的计算机不是大磁盘 VM，在[这些情况下](#special-configurations)，也无法在同一备份 VM 上运行脚本。

### <a name="os-requirements-on-the-machine"></a>对计算机的 OS 要求

需要执行脚本的计算机必须满足[这些 OS 要求](#system-requirements)。

### <a name="access-requirements-for-the-machine"></a>对计算机的访问要求

需要执行脚本的计算机必须满足[这些访问要求](#access-requirements)。

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

脚本运行后，LVM 分区将装载到 (s) /disk (s) 在脚本输出中指定的物理卷中。 此过程将

1. 获取物理卷或磁盘中卷组名称的唯一列表
2. 然后列出这些卷组中的逻辑卷
3. 然后，将逻辑卷装载到所需的路径。

##### <a name="listing-volume-group-names-from-physical-volumes"></a>列出物理卷的卷组名称

列出卷组名称：

```bash
pvs -o +vguuid
```

此命令会列出所有物理卷 (包括在运行脚本) 、其对应的卷组名称以及 (Uuid) 的卷组的唯一用户 Id 之前存在的卷。 此命令的示例输出如下所示。

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

第一列 (PV) 显示物理卷，后续列显示了卷组的相关卷组名称、格式、属性、大小、可用空间和唯一 ID。 命令输出显示所有物理卷。 请参阅脚本输出，并识别与备份相关的卷。 在上面的示例中，脚本输出会显示/dev/sdf 和/dev/sdd。 因此， *datavg_db* 卷组属于脚本， *Appvg_new* 卷组属于该计算机。 最后一种做法是确保唯一的卷组名称应有一个唯一的 ID。

###### <a name="duplicate-volume-groups"></a>重复的卷组

在某些情况下，卷组名称在运行脚本后可以有2个 Uuid。 这意味着在执行脚本的计算机和备份 VM 中的卷组名称是相同的。 接下来，我们需要重命名备份的 Vm 卷组。 请看下面的示例。

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

脚本输出会显示/dev/sdg、/dev/sdh、/dev/sdm2。 因此，相应的 VG 名称是 Appvg_new 和 rootvg。 但计算机的 VG 列表中也存在相同的名称。 我们可以验证一个 VG 名称是否有两个 Uuid。

现在，我们需要为基于脚本的卷重命名 VG 名称，例如：/dev/sdg、/dev/sdh、/dev/sdm2。 若要重命名卷组，请使用以下命令

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

现在，我们有了所有具有唯一 Id 的 VG 名称。

###### <a name="active-volume-groups"></a>活动卷组

请确保与脚本的卷相对应的卷组处于活动状态。 以下命令用于显示活动的卷组。 检查此列表中是否存在该脚本的相关卷组。

```bash
vgdisplay -a
```  

否则，请使用以下命令激活卷组。

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>列出卷组中的逻辑卷

获取与脚本相关的 VGs 的唯一活动列表后，可以使用以下命令列出这些卷组中存在的逻辑卷。

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

此命令将每个逻辑卷的路径显示为 "LV Path"。

##### <a name="mounting-logical-volumes"></a>装载逻辑卷

将逻辑卷装载到所选的路径：

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> 不要使用 "mount-a"。 此命令装入 "/etc/fstab" 中所述的所有设备。 这可能意味着可能会装载重复的设备。 数据可以重定向到脚本创建的、不保存数据的设备，因此可能会导致数据丢失。

#### <a name="for-raid-arrays"></a>对于 RAID 阵列

以下命令显示有关所有 RAID 磁盘的详细信息：

```bash
#!/bin/bash
mdadm –detail –scan
```

 相关 RAID 磁盘显示为 `/dev/mdm/<RAID array name in the protected VM>`

如果 RAID 磁盘具有物理卷，请使用 mount 命令：

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

如果 RAID 磁盘中配置了另一 LVM，请使用前述 LVM 分区相关过程，但使用卷名称代替 RAID 磁盘名称。

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
| openSUSE | 42.2 及更高版本 |

> [!NOTE]
> 我们在装有 SLES 12 SP4 OS 的计算机上运行文件恢复脚本时发现一些问题，我们正在与 SLES 团队一起进行调查。
> 目前，可在使用 SLES 12 SP2 和 SP3 OS 版本的计算机上正常运行文件恢复脚本。
>

该脚本还需要 Python 和 bash 组件才能执行并安全地连接到恢复点。

|组件 | 版本  |
| --------------- | ---- |
| bash | 4 及更高版本 |
| Python | 2.6.6 及更高版本  |
| TLS | 应支持 1.2  |

## <a name="access-requirements"></a>访问要求

如果在访问受限的计算机上运行该脚本，请确保有权访问：

- `download.microsoft.com`
- 恢复服务 Url (地理名称引用恢复服务保管库所在的区域) 
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` Azure 公共区域的 () 
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`（适用于 Azure 中国世纪互联）
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`（适用于 Azure 美国政府）
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`（适用于 Azure 德国）
- 出站端口 53 (DNS)、443、3260

> [!NOTE]
>
> - 下载的脚本文件名将具有要在 URL 中填充的地区名称。 例如：下载的脚本名称以 \' VMname \' \_ \' geoname \' _ GUID 开头 \' \' ，如*ContosoVM_wcus_12345678*
> - URL 则为“<https://pod01-rec2.wcus.backup.windowsazure.com>”
>

在 Linux 上，该脚本需要“open-iscsi”和“lshw”组件才能连接到恢复点。 如果这些组件不存在于运行脚本的计算机上，该脚本会请求权限以安装组件。 请同意安装必需组件。

需要访问 `download.microsoft.com`，才能下载用于在运行脚本的计算机与恢复点中的数据之间构建安全通道的组件。

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>从具有大磁盘的虚拟机备份恢复文件

本部分介绍如何从包含16个以上磁盘的 Azure 虚拟机的备份执行文件恢复，或者每个磁盘大小大于 4 TB。

由于文件恢复过程会将所有磁盘附加到备份中，因此当大量磁盘 ( # B0 16) 或大磁盘时 ( # 3 TB 每个) 时，建议使用以下操作点：

- 保留单独的还原服务器 (Azure VM D2v3 VM) 用于文件恢复。 只能将它用于文件恢复，并在不需要时将其关闭。 不建议在原始计算机上进行还原，因为它会对 VM 本身造成重大影响。
- 然后运行该脚本一次，检查文件恢复操作是否成功。
- 如果文件恢复进程挂起（磁盘从未装载或装载后未显示卷），请执行以下步骤。
  - 如果还原服务器是 Windows VM：
    - 确保 OS 为 WS 2012 或更高版本。
    - 确保在还原服务器中按以下建议设置注册表项，并确保重新启动服务器。 GUID 旁边的数字的范围为 0001 - 0005。 下面的示例中采用的是 0004。 浏览注册表项路径，直到参数部分。

    ![注册表项更改](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 如果还原服务器是 Linux VM：
  - 在文件 /etc/iscsi/iscsid.conf 中，将设置从
    - `node.conn[0].timeo.noop_out_timeout = 5`  自 `node.conn[0].timeo.noop_out_timeout = 30`
- 完成上述更改后，请再次运行脚本。 进行这些更改后，文件恢复成功的可能性很高。
- 用户每次下载脚本时，Azure 备份将开始准备用于下载的恢复点。 对于大磁盘，此过程需要相当长的时间。 如果连续出现大量请求，目标准备将造成下载激增。 因此，建议从门户/PowerShell/CLI 下载脚本，等待 20 - 30 分钟（探索性步骤），然后运行该脚本。 此时，目标应准备就绪，可以从脚本进行连接。
- 在文件恢复后，请确保返回到门户，然后选择无法在其中装入卷的恢复点的 " **卸载磁盘** "。 从本质上来说，此步骤将清理所有现有进程/会话并提高恢复的可能性。

## <a name="troubleshooting"></a>疑难解答

如果从虚拟机恢复文件时遇到问题，请查看下表了解更多信息。

| 错误消息/情景 | 可能的原因 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| 可执行文件输出：*连接到目标时捕获到异常* | 脚本无法访问恢复点    | 检查计算机是否满足[前述访问要求](#access-requirements)。 |  
| 可执行文件输出：已经通过 iSCSI 会话登录目标。 | 脚本已在同一台计算机上执行，并且已附加驱动器 | 已附加恢复点所在的卷。 不能使用与原始 VM 相同的驱动器号装载这些卷。 在文件的文件资源管理器中浏览所有可用卷。 |
| 可执行文件输出：此脚本无效，因为磁盘已通过门户卸载/已超过 12 小时限制。*请从门户下载新脚本。* |    磁盘已从门户卸除或已超过 12 小时的限制 | 此特定可执行文件现已失效，无法运行。 若要访问该恢复时间点的文件，请在门户中访问新的可执行文件。|
| 在运行可执行文件的计算机上：单击卸载按钮后，新卷没有卸载。 | 计算机上的 ISCSI 发起程序无响应/不刷新它与目标之间的连接，并且不保留缓存。 |  单击“卸载”后，请等待几分钟。 如果新卷未卸载，请浏览所有卷。 浏览所有卷会强制发起程序刷新连接并卸载卷，但会出现错误消息，指出磁盘不可用。|
| 可执行文件输出：脚本成功运行，但脚本输出中未显示“新卷已附加” |    这是暂时性的错误    | 卷其实已附加。 打开资源管理器即可浏览它们。 如果每次都使用同一台计算机来运行脚本，请考虑重启计算机，这样，以后运行可执行文件时应会显示列表。 |
| Linux 特定：无法查看所需的卷 | 运行脚本的计算机的 OS 可能无法识别受保护 VM 的基础文件系统 | 检查恢复点是崩溃一致还是文件一致。 如果文件一致，请在 OS 可识别受保护 VM 的文件系统的另一台计算机上运行该脚本。 |
| Windows 特定：无法查看所需的卷 | 磁盘可能已附加，但未配置卷。 | 从磁盘管理屏幕中，识别与恢复点相关的其他磁盘。 如果这些磁盘中有任何一个处于脱机状态，请尝试通过右键单击该磁盘并选择 " **联机**" 来使其联机。|

## <a name="security"></a>安全性

本部分介绍从 Azure VM 备份进行文件恢复时采取的各种安全措施。

### <a name="feature-flow"></a>功能流

构建此功能是为了在不需要还原整个 VM 或 VM 磁盘的情况下访问 VM 数据，同时尽量减少步骤数。 对 VM 数据的访问权限由脚本（在按如下方式运行时会装载恢复卷）提供，因此它是所有安全实现的基础：

  ![安全功能流](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>安全实现

#### <a name="select-recovery-point-who-can-generate-script"></a>选择恢复点（谁可以生成脚本）

此脚本可以访问 VM 数据，必须控制谁可以首先生成它，这很重要。 你需要登录到 Azure 门户，并且需要[获得 RBAC 授权](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)才能生成脚本。

文件恢复所需的授权级别与 VM 还原和磁盘还原相同。 换句话说，只有经过授权的用户才能查看 VM 数据和生成脚本。

生成的脚本使用适用于 Azure 备份服务的 Microsoft 官方证书签名。 篡改此脚本意味着破坏签名，尝试运行此脚本会被 OS 突出显示为潜在的风险。

#### <a name="mount-recovery-volume-who-can-run-script"></a>装载恢复卷（谁可以运行脚本）

只有管理员可以运行此脚本，并且应该以提升模式运行它。 此脚本仅运行预先生成的一组步骤，不接受任何外部源的输入。

若要运行此脚本，需要提供密码，该密码仅在 Azure 门户或 PowerShell/CLI 中生成脚本时显示给经授权的用户。 这是为了确保下载该脚本的授权用户同时负责运行该脚本。

#### <a name="browse-files-and-folders"></a>浏览文件和文件夹

为了浏览文件和文件夹，该脚本使用计算机中的 iSCSI 发起程序并连接到配置为 iSCSI 目标的恢复点。 此处，你可以想象用户尝试模拟/仿冒任一/所有组件的情景。

我们使用 CHAP 相互身份验证机制，让每个组件相互进行身份验证。 这意味着，身份虚假的发起程序很难连接到 iSCSI 目标，虚假目标也很难连接到运行脚本的计算机。

恢复服务与计算机之间的数据流由通过 TCP 构建安全 TLS 隧道提供保护（在运行脚本的计算机上[应支持 TLS 1.2](#system-requirements)）。

父/备份 VM 中 (ACL) 的任何文件访问控制列表也会保留在已装载的文件系统中。

此脚本提供对恢复点的只读访问权限，并且仅在 12 小时内有效。 如果要删除之前的访问权限，请登录 Azure 门户/PowerShell/CLI，并为该特定恢复点执行 **卸载磁盘** 。 脚本将立即失效。

## <a name="next-steps"></a>后续步骤

- 对于还原文件时遇到的任何问题，请参阅[疑难解答](#troubleshooting)部分
- 了解如何[通过 PowerShell 还原文件](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- 了解如何[通过 Azure CLI 还原文件](./tutorial-restore-files.md)
- 恢复 VM 后，了解如何[管理备份](./backup-azure-manage-vms.md)
