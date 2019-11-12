---
title: 为 Azure NetApp 文件配置 NFSv 4.1 默认域 |Microsoft Docs
description: 介绍如何配置 NFS 客户端，以便将 NFSv 4.1 与 Azure NetApp 文件配合使用。
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
ms.sourcegitcommit: f226cdd6406372b5693d46b6d04900f2f0cda4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906281"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv 4.1 默认域

NFSv4 介绍身份验证域的概念。 Azure NetApp 文件目前支持从服务到 NFS 客户端的仅限根目录的用户映射。 若要在 Azure NetApp 文件中使用 NFSv 4.1 功能，需要更新 NFS 客户端。

## <a name="default-behavior-of-usergroup-mapping"></a>用户/组映射的默认行为

默认情况下，根映射默认为 `nobody` 用户，因为 NFSv4 域设置为 `localdomain`。 当你将 NFSv 4.1 卷的 Azure NetApp 文件装载为根时，你将看到如下所示的文件权限：  

![NFSv 4.1 的用户/组映射的默认行为](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上面的示例所示，`file1` 的用户应 `root`，但默认情况下它将映射到 `nobody`。  本文介绍如何将 `file1` 用户设置为 `root`。  

## <a name="steps"></a>步骤 

1. 编辑 NFS 客户端上的 `/etc/idmapd.conf` 文件。   
    取消注释行 `#Domain` （即从行中删除 `#`），并将值 `localdomain` 更改为 `defaultv4iddomain.com`。 

    初始配置： 
    
    ![NFSv 4.1 的初始配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新的配置：
    
    ![更新了 NFSv 4.1 的配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸载任何当前装载的 NFS 卷。
3. 更新 `/etc/idmapd.conf` 文件。
4. 在主机上重新启动 `rpcbind` 服务（`service rpcbind restart`），或只重新启动主机。
5. 根据需要装载 NFS 卷。   

    请参阅[装入或卸载 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。 

以下示例显示了生成的用户/组更改： 

![NFSv 4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如示例所示，用户/组现已从 `nobody` 更改为 `root`。

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他（非根）用户和组的行为

Azure NetApp 文件支持在 NFSv 4.1 卷中具有与文件或文件夹关联的权限的本地用户（在主机上创建的用户）。 但是，该服务目前不支持将用户/组映射到多个节点。 因此，在一个主机上创建的用户在默认情况下不会映射到在另一台主机上创建的用户。 

在下面的示例中，`Host1` 有三个现有测试用户帐户（`testuser01`、`testuser02``testuser03`）： 

![NFSv 4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

在 `Host2`上，请注意，尚未创建测试用户帐户，但这两个主机上装载了相同的卷：

![NFSv 4.1 的生成配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>后续步骤 

[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

