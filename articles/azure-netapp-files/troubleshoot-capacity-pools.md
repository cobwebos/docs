---
title: 排查 Azure NetApp 文件的容量池问题 |Microsoft Docs
description: 介绍管理容量池时可能遇到的潜在问题，并提供问题的解决方案。
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651048"
---
# <a name="troubleshoot-capacity-pool-issues"></a>排查容量池问题

本文介绍管理容量池时可能遇到的问题的解决方法。 

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法 

|     添加状态    |     解决方法    |
|-|-|
| 创建容量池时出现问题 |  请确保容量池计数不超过限制。 请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)。  如果计数低于限制并且仍遇到问题，请提交支持票证并指定容量池名称。 |
| 删除容量池时出现问题  |  请确保在尝试删除容量池的订阅中删除所有 Azure NetApp 文件的卷和快照。 <br> 如果已删除所有卷和快照并且仍无法删除容量池，则对资源的引用可能仍然存在，而不会在门户中显示。 在这种情况下，请提交支持票证，并指定已执行上述建议步骤。 |
| 卷创建或修改失败，出现 `Requested throughput not available` 错误 | 卷的可用吞吐量取决于容量池的大小和服务级别。 如果没有足够的吞吐量，则应增加池大小或调整现有的卷吞吐量。 | 

## <a name="next-steps"></a>后续步骤  

* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
