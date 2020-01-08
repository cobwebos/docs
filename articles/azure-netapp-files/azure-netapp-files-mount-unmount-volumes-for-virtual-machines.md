---
title: 为虚拟机装载 Azure NetApp 文件卷
description: 了解如何在 Azure 中装载或卸载 Windows 虚拟机或 Linux 虚拟机的卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551533"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>为 Windows 或 Linux 虚拟机装载或卸载卷 

可以根据需要为 Windows 或 Linux 虚拟机装载或卸载卷。  Azure NetApp 文件上提供了 Linux 虚拟机的装载说明。  

1. 单击 "**卷**" 边栏选项卡，然后选择要装入的卷。 
2. 单击所选卷中的 "**装载说明**"，然后按照说明来装载卷。 

    ![装载说明 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![装载说明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    如果使用的是 NFSv 4.1，请使用以下命令来装载文件系统： `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. 如果希望在启动或重新启动 Azure VM 时自动装载 NFS 卷，请将条目添加到主机上的 `/etc/fstab` 文件中。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` 是在 "卷属性" 边栏选项卡中找到的 Azure NetApp 文件卷的 IP 地址。
    * `$FILEPATH` 是 Azure NetApp 文件卷的导出路径。
    * `$MOUNTPOINT` 是在 Linux 主机上创建的用于装载 NFS 导出的目录。

4. 如果要使用 NFS 将卷装载到 Windows，请执行以下操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 首先将卷装载到 Unix 或 Linux VM。  
    b.保留“数据库类型”设置，即设置为“共享”。 对卷运行 `chmod 777` 或 `chmod 775` 命令。  
    c. 通过 Windows 上的 NFS 客户端装载卷。

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件配置 NFSv 4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常见问题](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
