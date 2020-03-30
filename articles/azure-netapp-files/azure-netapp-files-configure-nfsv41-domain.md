---
title: 为 Azure NetApp 文件配置 NFSv4.1 默认域 |微软文档
description: 描述如何配置 NFS 客户端，以便使用 NFSv4.1 与 Azure NetApp 文件。
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
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906281"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv 4.1 默认域

NFSv4 引入了身份验证域的概念。 Azure NetApp 文件当前支持从服务到 NFS 客户端的纯根用户映射。 要将 NFSv4.1 功能与 Azure NetApp 文件一起使用，您需要更新 NFS 客户端。

## <a name="default-behavior-of-usergroup-mapping"></a>用户/组映射的默认行为

根映射默认给用户，`nobody`因为 NFSv4 域设置为`localdomain`。 当您将 Azure NetApp 文件 NFSv4.1 卷装载为根卷时，您将看到文件权限，如下所示：  

![NFSv4.1 的用户/组映射的默认行为](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上述示例所示，用户应`file1`为`root`，但它默认映射到`nobody`。  本文介绍如何将`file1`用户设置为`root`。  

## <a name="steps"></a>步骤 

1. 编辑`/etc/idmapd.conf`NFS 客户端上的文件。   
    `#Domain`取消注释行（`#`即从行中删除），并将值`localdomain`更改为`defaultv4iddomain.com`。 

    初始配置： 
    
    ![NFSv4.1 的初始配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新的配置：
    
    ![NFSv4.1 的更新配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸载当前装载的任何 NFS 卷。
3. 更新 `/etc/idmapd.conf` 文件。
4. 重新启动主机`rpcbind`上的服务 （），`service rpcbind restart`或只是重新启动主机。
5. 根据需要安装 NFS 卷。   

    请参阅[为 Windows 或 Linux 虚拟机安装或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。 

以下示例显示生成的用户/组更改： 

![NFSv4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如示例所示，用户/组现在已更改为`nobody`。 `root`

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他（非根）用户和组的行为

Azure NetApp 文件支持具有与 NFSv4.1 卷中文件或文件夹关联的权限的本地用户（在主机上本地创建的用户）。 但是，该服务当前不支持跨多个节点映射用户/组。 因此，默认情况下，在一台主机上创建的用户不会映射到在另一台主机上创建的用户。 

在下面的示例中，`Host1`有三个现有的测试用户帐户 （， `testuser02` `testuser03``testuser01`， 。 

![NFSv4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

在`Host2`上 请注意，尚未创建测试用户帐户，但两个主机上都装载了相同的卷：

![NFSv4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>后续步骤 

[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

