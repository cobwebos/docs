---
title: 为虚拟机安装 Azure NetApp 文件卷
description: 了解如何在 Azure 中装载或卸载 Windows 虚拟机或 Linux 虚拟机的卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551533"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>为 Windows 或 Linux 虚拟机装载或卸载卷 

可以根据需要为 Windows 或 Linux 虚拟机装载或卸载卷。  Azure NetApp 文件上提供了 Linux 虚拟机的装载说明。  

1. 单击"**卷"** 边栏选项卡，然后选择要为其装载的卷。 
2. 单击"装载所选卷**中的说明**"，然后按照说明装载卷。 

    ![安装指令 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![安装说明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    如果使用 NFSv4.1，请使用以下命令来装载文件系统：`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果要在启动或重新启动 Azure VM 时自动装载 NFS 卷，请向主机上`/etc/fstab`的文件添加一个条目。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`是在卷属性边栏选项卡中找到的 Azure NetApp 文件卷的 IP 地址。
    * `$FILEPATH`是 Azure NetApp 文件卷的导出路径。
    * `$MOUNTPOINT`是在用于装载 NFS 导出的 Linux 主机上创建的目录。

4. 如果要使用 NFS 将卷装载到 Windows：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 首先将卷安装到 Unix 或 Linux VM 上。  
    b.保留“数据库类型”设置，即设置为“共享”。 对卷`chmod 777`运行`chmod 775`或 命令。  
    c. 通过 Windows 上的 NFS 客户端将卷安装。

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件配置 NFSv 4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常见问题解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
