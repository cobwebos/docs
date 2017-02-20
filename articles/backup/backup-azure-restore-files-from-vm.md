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
ms.date: 2/6/2017
ms.author: pullabhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: 5c31fcac727a1206cb416cbe7340392a2816729a
ms.openlocfilehash: 93b689c132ec7e391a9107e4e3eb73a48e3ba09d


---
# <a name="recover-files-from-azure-virtual-machine-backup-preview"></a>从 Azure 虚拟机备份恢复文件（预览版）

Azure 备份提供从 Azure VM 备份还原 [Azure VM 和磁盘](./backup-azure-arm-restore-vms.md)的功能。 本文介绍如何从 Azure VM 备份恢复文件和文件夹等项。

> [!Note]
> 此功能适用于使用 Resource Manager 模型部署的、在恢复服务保管库中受保护的 Azure VM。
> 目前，从 Windows Azure VM 备份恢复文件的功能为预览版。 从 Linux Azure VM 恢复文件的功能即将推出。
目前，不支持从加密的 VM 备份恢复文件。
>

## <a name="mount-the-volume-and-copy-files"></a>装载卷并复制文件

1. 登录到 [Azure 门户](http://portal.Azure.com)。 找到相关的恢复服务保管库和所需的备份项。

2. 在“备份项”边栏选项卡中，单击“文件恢复(预览版)”

    ![打开恢复服务保管库备份项](./media/backup-azure-restore-files-from-vm/open-vault-item.png)

    此时将打开“文件恢复”边栏选项卡。

    ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

3. 从“选择恢复点”下拉菜单中，选择包含所需文件的恢复点。 默认已选择最新的恢复点。

4. 单击“下载可执行文件”，下载用于从恢复点复制文件的软件。

  该可执行文件是一个脚本，可在本地计算机与指定的恢复点之间创建连接。

5. 在要恢复文件的计算机上，运行该可执行文件。 必须使用管理员凭据运行该脚本。 如果在访问受限的计算机上运行该脚本，请确保能够访问：

    - go.microsoft.com
    - 用于 Azure VM 备份的 Azure 终结点
    - 出站端口 3260

    ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/executable-output.png)

    可以在与生成恢复点时所用的计算机具有相同（或兼容）操作系统的任何计算机上运行该脚本。 有关兼容的操作系统，请参阅[兼容的 OS 表](backup-azure-restore-files-from-vm.md#compatible-os)。 如果受保护的 Azure 虚拟机使用 Windows 存储空间，则你不能在此虚拟机上运行该可执行脚本。 应该在使用 Windows 存储空间的其他任何计算机上运行该可执行脚本。 建议在使用兼容操作系统的计算机上运行该可执行脚本。

    ![文件恢复边栏选项卡](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

### <a name="compatible-os"></a>兼容的 OS

下表显示了服务器与计算机操作系统之间的兼容性。 恢复文件时，无法在不兼容的操作系统之间还原文件。

|服务器 OS | 兼容的客户端 OS  |
| --------------- | ---- |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |


### <a name="drive-letter-assignments"></a>驱动器号分配

运行该脚本时，操作系统将装载新卷并分配驱动器号。 可以使用 Windows 资源管理器或文件资源管理器来浏览这些驱动器。 分配给卷的驱动器号不能与原始虚拟机中的驱动器号相同，不过，卷名将会保留。 例如，如果原始虚拟机上的卷为“数据磁盘(E:\)”，可在本地计算机上将该卷附加为“数据磁盘(‘任何可用的驱动器’:\)”。 浏览脚本输出中所述的所有卷，直到找到你的文件/文件夹。  


## <a name="closing-the-connection"></a>关闭连接

识别文件并将其复制到本地存储位置后，请删除（或卸载）其他驱动器。 若要卸载驱动器，请在 Azure 门户中的“文件恢复”边栏选项卡上，单击“卸载磁盘”。

![卸载磁盘](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

卸载磁盘后，将显示一条消息，指出卸载成功。 连接可能在几分钟时间后才会刷新，以便能够删除磁盘。

## <a name="windows-storage-spaces"></a>Windows 存储空间

Windows 存储空间是 Windows 存储中用于将存储虚拟化的一种技术。 使用 Windows 存储空间可将符合行业标准的磁盘分组到存储池中，然后基于这些存储池中的可用空间创建称为存储空间的虚拟磁盘。

如果备份的 Azure VM 使用 Windows 存储空间，则你不能在 VM 上运行该可执行脚本。 应该在使用 Windows 存储空间的其他任何计算机上运行该可执行脚本。 建议在使用兼容操作系统的计算机上运行该可执行脚本。

## <a name="troubleshooting"></a>故障排除

如果从虚拟机恢复文件时遇到问题，请查看下表了解更多信息。

| 错误消息/情景 | 可能的原因 | 建议的操作 |
| ------------------------ | -------------- | ------------------ |
| 可执行文件输出：“连接到目标时发生异常” |脚本无法访问恢复点    | 检查计算机是否满足上面所述的访问要求|  
|    可执行文件输出：“已经通过 ISCSI 会话登录目标。” |    脚本已在同一台计算机上执行，并且已附加驱动器 |    已附加恢复点所在的卷。 不能使用与原始 VM 相同的驱动器号装载这些卷。 在文件的文件资源管理器中浏览所有可用卷 |
| 可执行文件输出：“此脚本无效，因为磁盘已通过门户卸载/已超过 12 小时限制。请从门户下载新脚本。” |    磁盘已从门户卸载或超过了 12 小时限制 |    此特定可执行文件现已失效，无法运行。 若要访问该恢复时间点的文件，请在门户中访问新的可执行文件|
| 在运行可执行文件的计算机上：单击卸载按钮后，新卷不会卸载 |    计算机上的 ISCSI 发起程序无响应/不刷新它与目标之间的连接，并且不保留缓存 |    按下卸载按钮后，请等待几分钟。 如果仍然无法卸载新卷，请浏览所有卷。 这会强制发起程序刷新连接并卸载卷，但会出现错误消息，指出磁盘不可用|
| 可执行文件输出：脚本已成功运行，但脚本输出中不显示“已附加新卷” |    这是暂时性的错误    | 卷其实已附加。 打开资源管理器即可浏览它们。 如果每次都使用同一台计算机来运行脚本，请考虑重新启动计算机，这样，以后运行可执行文件时应会显示列表。 |



<!--HONumber=Feb17_HO1-->


