---
title: 排查 Azure NetApp 文件的跨区域复制问题 |Microsoft Docs
description: 介绍可帮助你对 Azure NetApp 文件的跨区域复制问题进行故障排除的错误消息和解决方案。
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
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 3aaa5d2bc6fdbda0d1db212539c719aa65cae61b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708638"
---
# <a name="troubleshoot-cross-region-replication"></a>跨区域复制故障排除

本文介绍可帮助你解决 Azure NetApp 文件的跨区域复制问题的错误消息和解决方法。 

## <a name="errors-creating-replication"></a>创建复制时出错  

|     错误消息    |     解决方法    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     不能使用已经处于数据复制关系中的源卷创建复制。    |
|     `Peered region   '{0}' is not accepted`    |     你正在尝试在对等互连区域之间创建复制。    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     验证远程资源 ID 是卷资源 ID。    |

## <a name="errors-authorizing-volume"></a>授权卷的错误  

|     错误消息    |     解决方法    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`UI 或 API 请求中缺少或无效 (修复错误消息) 。    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`UI 或 API 请求中缺少或无效。    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     验证   `RemoteResourceID` 用户是否正确或是否存在。    |
|     `Remote volume   '{0}' is not configured for replication`    |     目标卷不是数据保护卷。    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     数据保护卷的远程资源 ID 中没有此源卷 () 输入错误的源 ID。    |
|     `The   destination volume replication creation failed (message: {0})`    |     此错误表示服务器错误。 联系人 支持。    |

## <a name="errors-deleting-replication"></a>删除复制时出错

|     错误消息    |     解决方法    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     验证复制是否已损坏，或者它未初始化且空闲 (初始化失败) 。    |
|     `Cannot delete   source replication`    |     不允许从源端删除复制。 请确保从目标端删除复制。    |

## <a name="errors-resyncing-volume"></a>错误重新同步卷

|     错误消息    |     解决方法    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     验证卷复制是否处于 "已中断" 状态。    |

## <a name="errors-deleting-snapshot"></a>删除快照时出错 

|     错误消息    |     解决方法    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     如果要删除此快照，请验证是否已将卷的复制中断。    |
|     `Cannot delete   volume replication generated snapshot`    |     不允许删除复制基线快照。    |

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [创建复制对等互连](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)
