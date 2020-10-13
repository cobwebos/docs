---
title: 排查 Azure NetApp 文件的快照策略问题 |Microsoft Docs
description: 介绍可帮助你解决 Azure NetApp 文件的快照策略管理问题的错误消息和解决方案。
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650998"
---
# <a name="troubleshoot-snapshot-policies"></a>快照策略问题故障排除

本文介绍了在管理 Azure NetApp 文件快照策略时可能会遇到的错误情况。 它还提供可帮助您解决问题的解决方案。

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法 

|     添加状态    |     解决方法    |
|-|-|
| 快照策略创建失败，快照策略名称无效。 | 如果快照策略名称无效，则会在创建快照策略的过程中出错。 以下准则适用于快照策略名称：  <ul><li> 快照策略名称不能包含非 ASCII 字符或特殊字符。 </li> <li> 快照策略名称必须以字母或数字开头，并且只能包含字母、数字、下划线 ( "_" ) 和连字符 ( "-" ) 。 </li> <li> 快照策略名称的长度必须介于1到64个字符之间。  </li></ul> 根据指导原则修改快照策略名称。  |
| 快照策略创建失败，值无效。 | 如果为字段（如或）输入的值无效，Azure NetApp 文件将无法创建快照策略 `Number of snapshots to keep` `Minute on the hour to take snapshot` 。 有效值如下：  <ul><li>该值必须是有效的数字。</li> <li>该值必须介于0到59之间。</li></ul> 请确保为字段提供了有效值。 | 
| 快照策略创建失败并出现错误 `Total number of snapshots to keep exceeds 255` 。 | 每个卷 [最多可以有255个快照](azure-netapp-files-resource-limits.md)。 最大值包括每小时、每日、每周和每月快照的总和。 <br> 请减小 `Snapshots to keep` 该值，然后重试。 |
| 将策略分配到卷失败并出现错误 `Total snapshot policy is over the max '255'` 。 | 每个卷 [最多可以有255个快照](azure-netapp-files-resource-limits.md)。 当所有按需、每小时、每日、每周和每月快照的总和超过最大值时，将发生错误。 <br> 请减小 `snapshots to keep` 值或删除一些按需快照，然后重试。 | 

## <a name="next-steps"></a>后续步骤  

* [管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
