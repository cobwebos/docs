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
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341376"
---
# <a name="troubleshoot-snapshot-policies"></a>快照策略疑难解答

本文介绍了在管理 Azure NetApp 文件快照策略时可能会遇到的错误情况。 它还提供可帮助您解决问题的解决方案。

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>快照策略创建失败，快照策略名称无效

如果快照策略名称无效，则会在创建快照策略的过程中出错。 以下准则适用于快照策略名称：  

* 快照策略名称不能包含非 ASCII 字符或特殊字符。
* 快照策略名称必须以字母或数字开头，并且只能包含字母、数字、下划线 ( "_" ) 和连字符 ( "-" ) 。
* 快照策略名称的长度必须介于1到64个字符之间。  

应根据指南来修改快照策略名称。  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>快照策略创建失败，值无效 

如果为字段（如或）输入的值无效，Azure NetApp 文件将无法创建快照策略 `Number of snapshots to keep` `Minute on the hour to take snapshot` 。  
 
有效值如下：   

* 该值必须是有效的数字。
* 该值必须介于0到59之间。

请确保为字段提供了有效值。

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>快照策略创建失败，出现 "要保留的快照总数超过 255" 错误 

每个卷 [最多可以有255个快照](azure-netapp-files-resource-limits.md)。 最大值包括每小时、每日、每周和每月快照的总和。 应减小 `Snapshots to keep` 该值，然后重试。

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>将策略分配到卷失败，出现错误 "快照策略总数超过最大值" 255 "错误

每个卷 [最多可以有255个快照](azure-netapp-files-resource-limits.md)。 当所有按需、每小时、每日、每周和每月快照的总和超过最大值时，将发生错误。 请减小 `snapshots to keep` 值或删除一些按需快照，然后重试。

## <a name="next-steps"></a>后续步骤  

* [管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
