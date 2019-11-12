---
title: 为 Windows 或 Linux 虚拟机装载或卸载 Azure NetApp 文件卷 | Microsoft Docs
description: 说明如何为 Windows 虚拟机或 Linux 虚拟机装载或卸载卷。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 49c5fc4a9d4fed2176baf220e3fcc8f887757a36
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906226"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>为 Windows 或 Linux 虚拟机装载或卸载卷 

可以根据需要为 Windows 或 Linux 虚拟机装载或卸载卷。  Azure NetApp 文件上提供了 Linux 虚拟机的装载说明。  

1. 单击 "**卷**" 边栏选项卡，然后选择要装入的卷。 
2. 单击所选卷中的 "**装载说明**"，然后按照说明来装载卷。 

    ![装载说明 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![装载说明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
如果使用的是 NFSv 4.1，请使用以下命令来装载文件系统：  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

请参阅[为 Azure NetApp 文件配置 nfsv 4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)。