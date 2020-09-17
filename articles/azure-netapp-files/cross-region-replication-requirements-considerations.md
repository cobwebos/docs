---
title: 使用 Azure NetApp 文件时的要求和注意事项卷跨区域复制 |Microsoft Docs
description: 描述使用 Azure NetApp 文件的卷跨区域复制功能的要求和注意事项。
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708547"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>使用跨区域复制的要求和注意事项 

请注意以下要求以及有关使用 Azure NetApp 文件的 [卷跨区域复制](cross-region-replication-create-peering.md) 功能的注意事项：  

## <a name="requirements-and-considerations"></a>要求和注意事项 

* 跨区域复制功能目前为公共预览版。 需要提交候补请求，以便通过 [Azure NetApp 文件跨区域复制候补提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 使用跨区域复制功能之前，请先从 Azure NetApp 文件团队等待官方确认电子邮件。
* Azure NetApp 文件复制仅适用于某些固定区域对。 请参阅 [支持的区域对](cross-region-replication-introduction.md#supported-region-pairs)。 
* SMB 卷与 NFS 卷一起受支持。 复制 SMB 卷需要在源和目标 NetApp 帐户中建立 Active Directory 连接。 目标 AD 连接必须具有对 DNS 服务器的访问权限，或者添加可从目标区域中的委托子网访问的域控制器。 有关详细信息，请参阅 [Active Directory 连接的要求](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)。 
* 目标帐户必须与源卷区域在不同的区域中。 你还可以在不同的区域中选择现有的 NetApp 帐户。  
* Azure NetApp 文件复制当前不支持多个订阅;所有复制都必须在单个订阅下执行。
* 最多可为每个区域的单个订阅中的复制设置五个卷。 你可以打开支持票证，请求在区域) 中 (每个订阅的5个复制目标卷的默认配额增加。 
* 此接口可能会有最多五分钟的延迟，以反映源卷上新添加的快照。  
* 不支持级联和扇入/输出拓扑。
* 目前不支持为从快照创建的源卷配置卷复制。
* 设置跨区域复制后，复制过程将创建 *snapmirror 快照* 以提供源卷和目标卷之间的引用。 为每次增量传输创建新的快照时，将自动循环使用 Snapmirror 快照。 在删除复制关系和卷之前，不能删除 snapmirror 快照。 
* 当复制关系处于活动或中断状态时，以及在删除复制关系之后，可以删除复制关系的源卷上的手动快照。 在复制关系中断之前，不能删除目标卷的手动快照。

## <a name="next-steps"></a>后续步骤
* [创建复制对等](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况状态](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)


