---
title: Azure NetApp 文件的手动 QoS 容量池 |Microsoft Docs
description: 介绍手动 QoS 容量池，并提供其他信息的参考。
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935700"
---
# <a name="manual-qos-capacity-pool"></a>手动 QoS 容量池

本文介绍了手动服务质量 (QoS) 容量池功能。

## <a name="how-manual-qos-differs-from-auto-qos"></a>手动 QoS 不同于自动 QoS 的方式

[QoS 类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)是容量池的属性。 Azure NetApp 文件提供两种 QoS 类型的容量池–自动 (默认) 和手动。  

在 *手动* QoS 容量池中，可以单独为卷分配容量和吞吐量。 使用手动 QoS 容量池创建的所有卷的总吞吐量受池总吞吐量的限制。 它由池大小和服务级别吞吐量共同决定。 

在 *自动* QoS 容量池中，吞吐量会自动分配到池中的卷，与分配给卷的大小配额成正比。  

请参阅 [Azure Netapp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 和 [azure Netapp 文件的性能注意事项](azure-netapp-files-performance-considerations.md) ，了解有关 QoS 类型的注意事项。

## <a name="how-to-specify-the-manual-qos-type"></a>如何指定手动 QoS 类型

[创建容量池](azure-netapp-files-set-up-capacity-pool.md)时，可为容量池指定使用手动 QoS 类型。  还可以 [更改现有容量池](manage-manual-qos-capacity-pool.md#change-to-qos) 以使用手动 QoS 类型。 

将容量类型设置为手动 QoS 是永久性更改。 不能将手动 QoS 类型容量工具转换为自动 QoS 容量池。 

使用手动 QoS 类型需要 [注册该功能](manage-manual-qos-capacity-pool.md#register-the-feature)。  

## <a name="next-steps"></a>后续步骤

* [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的性能考虑因素](azure-netapp-files-performance-considerations.md)
* [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [创建 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)
* [排查容量池问题](troubleshoot-capacity-pools.md)
