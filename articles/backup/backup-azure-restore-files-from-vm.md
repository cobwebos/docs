---
title: "Azure 备份：从 Azure VM 备份恢复文件和文件夹 | Microsoft 文档"
description: "从 Azure 虚拟机恢复点恢复文件"
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: "项目级恢复; 从 Azure VM 备份恢复文件; 从 Azure VM 还原文件"
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/20/2017
ms.author: pullabhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: ae7c345c11a7db25413d60ad822f16f84ca37362
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>从 Azure 虚拟机备份恢复文件

Azure 备份提供从 Azure VM 备份还原 [Azure VM 和磁盘](./backup-azure-arm-restore-vms.md)的功能。 本文介绍如何从 Azure VM 备份恢复文件和文件夹等项。

> [!Note]
> 此功能适用于使用 Resource Manager 模型部署的、在恢复服务保管库中受保护的 Azure VM。
> 不支持从加密的 VM 备份恢复文件。
>

## <a name="mount-the-volume-and-copy-files"></a>装载卷并复制文件

1. 登录到 [Azure 门户](http://portal.Azure.com)。 找到相关的恢复服务保管库和所需的备份项。

2. 在“备份项”边栏选项卡中，单击“文件恢复”

    ![打开恢复服务保管库备份项](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    此时将打开“文件恢复”边栏选项卡。

    ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. 从“选择恢复点”下拉菜单中，选择包含所需文件的恢复点。 默认已选择最新的恢复点。

4. 单击“下载可执行文件”（适用于 Windows Azure VM）或“下载脚本”（适用于 Linux Azure VM）以下载要用于从恢复点复制文件的软件。

  该可执行文件/脚本可在本地计算机与指定的恢复点之间创建连接。

5. 需要密码才能运行下载的脚本/可执行文件。 可以使用生成的密码旁的复制按钮从门户复制密码

    ![生成的密码](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

6. 在要恢复文件的计算机上，运行该可执行文件/脚本。 必须使用管理员凭据运行该可执行文件/脚本。 如果在访问受限的计算机上运行该脚本，请确保能够访问：

    - download.microsoft.com
    - 用于 Azure VM 备份的 Azure 终结点
    - 出站端口 3260

   在 Linux 上，该脚本需要“open-iscsi”和“lshw”组件才能连接到恢复点。 如果运行该脚本时这些组件未存在于计算机上，该脚本会请求安装相关组件的权限，并在获得许可后安装这些组件。
   
   出现提示时，输入从门户复制的密码。 输入有效的密码后，脚本将连接到恢复点。
      
    ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/executable-output.png)
    
   
   可以在具有与备份 VM 相同（或兼容）操作系统的任何计算机上运行该脚本。 有关兼容的操作系统，请参阅[兼容的 OS 表](backup-azure-restore-files-from-vm.md#compatible-os)。 如果受保护的 Azure 虚拟机使用 Windows 存储空间（适用于 Windows Azure VM）或 LVM/RAID 阵列（适用于 Linux VM），则不能在同一虚拟机上运行该可执行文件/脚本。 而应在具有兼容操作系统的任何其他计算机上运行它。

### <a name="compatible-os"></a>兼容的 OS

#### <a name="for-windows"></a>对于 Windows

下表显示了服务器与计算机操作系统之间的兼容性。 恢复文件时，无法在不兼容的操作系统之间还原文件。

|服务器 OS | 兼容的客户端 OS  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

#### <a name="for-linux"></a>对于 Linux

在 Linux 中，基本要求是运行该脚本的计算机的 OS 应支持备份 Linux VM 中存在的文件的文件系统。 选择用于运行该脚本的计算机时，请确保它具有下表中所述的兼容 OS 和版本。

|Linux OS | 版本  |
| --------------- | ---- |
| Ubuntu | 12.04 及更高版本 |
| CentOS | 6.5 及更高版本  |
| RHEL | 6.7 及更高版本 |
| Debian | 7 及更高版本 |
| Oracle Linux | 6.4 及更高版本 |

该脚本还需要 python 和 bash 组件才能执行并安全地连接到恢复点。

|组件 | 版本  |
| --------------- | ---- |
| bash | 4 及更高版本 |
| Python | 2.6.6 及更高版本  |


### <a name="identifying-volumes"></a>标识卷

#### <a name="for-windows"></a>对于 Windows

运行可执行文件时，操作系统将装载新卷并分配驱动器号。 可以使用 Windows 资源管理器或文件资源管理器来浏览这些驱动器。 分配给卷的驱动器号不能与原始虚拟机中的驱动器号相同，不过，卷名将会保留。 例如，如果原始虚拟机上的卷为“数据磁盘(E:\)”，可在本地计算机上将该卷附加为“数据磁盘(‘任何可用的驱动器’:\)”。 浏览脚本输出中所述的所有卷，直到找到你的文件/文件夹。  
       
   ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>对于 Linux

在 Linux 中，恢复点的卷将装载到运行脚本的文件夹。 将相应地显示附加的磁盘、卷和相应装载路径。 这些装载路径对于具有根级别访问权限的用户可见。 浏览脚本输出中涉及的卷。

  ![“Linux 文件恢复”边栏选项卡](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>关闭连接

识别文件并将其复制到本地存储位置后，请删除（或卸载）其他驱动器。 若要卸载驱动器，请在 Azure 门户中的“文件恢复”边栏选项卡上，单击“卸载磁盘”。

![卸载磁盘](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

卸载磁盘后，将显示一条消息，指出卸载成功。 连接可能在几分钟时间后才会刷新，以便能够删除磁盘。

在 Linux 中，断开与恢复点的连接后，OS 不会自动删除相应装载路径。 这些卷作为“孤立”卷存在并且可见，但当你访问/写入文件时将引发错误。 这些卷可以手动删除。 该脚本运行时将标识以前的任何恢复点存在的任何此类卷，并在获得许可后将其清除。

## <a name="special-configurations"></a>特殊配置

### <a name="dynamic-disks"></a>动态磁盘

如果已备份的 Azure VM 在动态磁盘上具有跨多个磁盘的卷（跨区卷和带区卷）和/或容错卷（镜像卷和 RAID-5 卷），则无法在同一 VM 上运行可执行脚本。 而应在具有兼容操作系统的任何其他计算机上运行该可执行脚本。

### <a name="windows-storage-spaces"></a>Windows 存储空间

Windows 存储空间是 Windows 存储中用于将存储虚拟化的一种技术。 使用 Windows 存储空间可将符合行业标准的磁盘分组到存储池中，然后基于这些存储池中的可用空间创建称为存储空间的虚拟磁盘。

如果备份的 Azure VM 使用 Windows 存储空间，则不能在此同一 VM 上运行该可执行脚本。 而应在具有兼容操作系统的任何其他计算机上运行该可执行脚本。

### <a name="lvmraid-arrays"></a>LVM/RAID 阵列

在 Linux 中，逻辑卷管理器 (LVM) 和/或软件 RAID 阵列用于管理多个磁盘上的逻辑卷。 如果备份 Linux VM 使用 LVM 和/或 RAID 阵列，则不能在此同一 VM 上运行该脚本。 而应在具有兼容 OS 且支持备份 VM 的文件系统的任何其他计算机上运行该脚本。

脚本输出将显示 LVM 和/或 RAID 阵列磁盘，以及具有如下所示分区类型的卷

   ![“Linux LVM 输出”边栏选项卡](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
用户需要运行以下命令以使这些分区联机。 

**对于 LVM 分区**

```
$ pvs <volume name as shown above in the script output> 
```
这将列出物理卷下的卷组名称。

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```
这将列出卷组中的所有逻辑卷名称及其路径。

```
$ mount <LV path> </mountpath>
```
将逻辑卷装载到所选的路径。


**对于 RAID 阵列**

```
$ mdadm –detail –scan
```
这将显示有关所有 RAID 磁盘的详细信息。 相关 RAID 磁盘将显示为 `/dev/mdm/<RAID array name in the backed up VM>`

如果 RAID 磁盘具有物理卷，请使用 mount 命令。
```
$ mount [RAID Disk Path] [/mounthpath]
```

如果此 RAID 磁盘配置有另一个 LVM，则对其卷名为 RAID 磁盘名称的 LVM 分区执行上述同一过程

## <a name="troubleshooting"></a>故障排除

如果从虚拟机恢复文件时遇到问题，请查看下表了解更多信息。

| 错误消息/情景 | 可能的原因 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| 可执行文件输出：“连接到目标时发生异常” |脚本无法访问恢复点 | 检查计算机是否满足上面所述的访问要求|  
|   可执行文件输出：“已经通过 ISCSI 会话登录目标。” | 脚本已在同一台计算机上执行，并且已附加驱动器 | 已附加恢复点所在的卷。 不能使用与原始 VM 相同的驱动器号装载这些卷。 在文件的文件资源管理器中浏览所有可用卷 |
| 可执行文件输出：“此脚本无效，因为磁盘已通过门户卸载/已超过 12 小时限制。请从门户下载新脚本。” |  磁盘已从门户卸除或超过了 12 小时限制 |    此特定可执行文件现已失效，无法运行。 若要访问该恢复时间点的文件，请在门户中访问新的可执行文件|
| 在运行可执行文件的计算机上：单击卸载按钮后，新卷不会卸载 |    计算机上的 ISCSI 发起程序无响应/不刷新它与目标之间的连接，并且不保留缓存 |    按下卸载按钮后，请等待几分钟。 如果仍然无法卸载新卷，请浏览所有卷。 这会强制发起程序刷新连接并卸载卷，但会出现错误消息，指出磁盘不可用|
| 可执行文件输出：脚本已成功运行，但脚本输出中不显示“已附加新卷” | 这是暂时性的错误   | 卷其实已附加。 打开资源管理器即可浏览它们。 如果每次都使用同一台计算机来运行脚本，请考虑重新启动计算机，这样，以后运行可执行文件时应会显示列表。 |
| Linux 特定：无法查看所需的卷 | 运行脚本的计算机的 OS 可能无法识别备份 VM 的基础文件系统 | 检查恢复点是崩溃一致还是文件一致。 如果是文件一致，请在其 OS 可识别备份 VM 的文件系统的另一台计算机上运行脚本 |
| Windows 特定：无法查看所需的卷 | 磁盘可能已附加，但未配置卷 | 从磁盘管理屏幕中，识别与恢复点相关的其他磁盘。 如果这些磁盘有任何一个处于脱机状态，请尝试通过右键单击该磁盘并单击“联机”来使其联机|

