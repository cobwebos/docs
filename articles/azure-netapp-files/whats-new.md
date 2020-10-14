---
title: Azure NetApp 文件中的新增功能 | Microsoft Docs
description: 总结 Azure NetApp 文件的最新新功能和增强功能。
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
ms.topic: overview
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 56ae3ac1331ed054a1fb808ae06e990b67c06bae
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932407"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp 文件中的新增功能

Azure NetApp 文件会定期更新。 本文总结了最新的新功能和增强功能。 

## <a name="september-2020"></a>2020 年 9 月

* [Azure NetApp 文件跨区域复制](cross-region-replication-introduction.md)（公共预览版）

  Azure NetApp 文件现在支持跨区域复制。 借助这项新的灾难恢复功能，可以快速且经济高效地将 Azure NetApp 文件卷从一个 Azure 区域复制到另一个 Azure 区域，从而保护数据免受不可预见的区域故障的影响。 Azure NetApp 文件跨区域复制采用 NetApp SnapMirror® 技术；只有已更改的块才以压缩、有效的格式通过网络发送。 这项专有技术最大程度地减少了跨区域复制所需的数据量，从而节省了数据传输成本。 它还可以缩短复制时间，让你可以实现较小的还原点目标 (RPO)。

* [手动 QoS 容量池](manual-qos-capacity-pool-introduction.md)（预览版）  

    在手动 QoS 容量池中，可以单独为卷分配容量和吞吐量。 使用手动 QoS 容量池创建的所有卷的总吞吐量受池总吞吐量的限制。 它由池大小和服务级别吞吐量共同决定。 另外，容量池的 [QoS 类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)也可以是自动，这是默认值。 在自动 QoS 容量池中，吞吐量会自动分配到池中的卷，并与分配给这些卷的大小配额成正比。

* [LDAP 签名](azure-netapp-files-create-volumes-smb.md)（预览版）   

    Azure NetApp 文件现在支持 LDAP 签名，以在 Azure NetApp 文件服务和用户指定的 Active Directory 域服务域控制器之间进行安全 LDAP 查找。 此功能目前处于预览状态。

* [用于 AD 身份验证的 AES 加密](azure-netapp-files-create-volumes-smb.md)（预览版）

    Azure NetApp 文件现在支持对与 DC 的 LDAP 连接进行 AES 加密，以便为 SMB 卷启用 AES 加密。 此功能目前处于预览状态。 

* 新[指标](azure-netapp-files-metrics.md)：   

    * 新卷指标： 
        * *卷分配大小*：卷的预配大小
    * 新池指标： 
        * *池分配大小*：池的预配大小 
        * *池的总快照大小*：池中所有卷的快照大小总和

## <a name="july-2020"></a>2020 年 7 月

* [双协议（NFSv3 和 SMB）卷](create-volumes-dual-protocol.md)

    现在可以创建 Azure NetApp 文件卷。该卷允许进行同步双协议（NFS v3 和 SMB）访问，并支持 LDAP 用户映射。 此功能支持以下用例：你可能具有基于 Linux 的工作负荷，该工作负荷在 Azure NetApp 文件卷中生成和存储数据。 同时，你的员工需要使用基于 Windows 的客户端和软件来分析同一 Azure NetApp 文件卷中新生成的数据。 有了同步双协议访问功能，就不必将工作负荷生成的数据复制到使用不同协议的单独卷中进行后期分析，从而节省了存储成本和运营时间。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并已正式发布。 有关详细信息，请参阅[同步双协议访问文档](create-volumes-dual-protocol.MD)。

* [NFS v4.1 Kerberos 传输中加密](configure-kerberos-encryption.MD)

    Azure NetApp 文件现在支持使用 AES-256 加密算法在 Kerberos 模式（krb5、krb5i 和 krb5p）下加密 NFS 客户端，从而提供额外的数据安全性。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并已正式发布。 有关详细信息，请参阅 [NFS v4.1 Kerberos 加密文档](configure-kerberos-encryption.MD)。

* [动态卷服务级别更改](dynamic-change-volume-service-level.MD)

    云保证了 IT 支出的灵活性。 现在，可以通过将现有 Azure NetApp 文件卷移到使用所需卷服务级别的另一个容量池中，来更改该卷的服务级别。 这种针对卷的就地服务级别更改不需要迁移数据， 也不会影响对卷的数据平面访问。 你可以将现有卷更改为使用更高的服务级别（提升性能），或使用更低的服务级别（优化成本）。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并且目前提供公共预览版。 你可以按照[动态卷服务级别更改文档](dynamic-change-volume-service-level.md)注册该预览版功能。

* [卷快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)（预览版） 

    Azure NetApp 文件可用于创建卷的时间点快照。 现在，你可以创建快照策略，使 Azure NetApp 文件按你选择的频率自动创建卷快照。 你可以安排以每小时、每天、每周或每月为周期拍摄快照。 还可以指定作为快照策略一部分保留的最大快照数。 此功能免费（常规 [Azure NetApp 文件存储成本](https://azure.microsoft.com/pricing/details/netapp/)仍然适用），并且目前提供预览版。 你可以按照[卷快照策略文档](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)注册该预览版功能。

* [NFS 根访问导出策略](azure-netapp-files-configure-export-policy.md)

    Azure NetApp 文件现在允许指定根帐户是否可以访问卷。 

* [隐藏快照路径](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp 文件现在允许指定用户是否可以查看和访问已装载卷上的 `.snapshot` 目录（NFS 客户端）或 `~snapshot` 文件夹（SMB 客户端）。

## <a name="may-2020"></a>2020 年 5 月

* [备份策略用户](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)（预览版）

    Azure NetApp 文件允许包含其他帐户，这些帐户要求对创建用于 Azure NetApp 文件的计算机帐户具有提升的权限。 将允许指定的帐户在文件或文件夹级别更改 NTFS 权限。 例如，你可以指定一个非特权服务帐户，用于将数据迁移到 Azure NetApp 文件中的 SMB 文件共享。 备份策略用户功能目前提供公共预览版。

## <a name="next-steps"></a>后续步骤
* [什么是 Azure NetApp 文件](azure-netapp-files-introduction.md)
* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
