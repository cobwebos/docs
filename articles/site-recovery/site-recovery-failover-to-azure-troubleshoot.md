---
title: "对故障转移到 Azure 的故障进行排除 | Microsoft 文档"
description: "本指南介绍如何解决在故障转移到 Azure 中的常见错误"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>解决从虚拟机到 Azure 的故障转移时出现的错误
在执行从虚拟机到 Azure 的故障转移时，可能会收到以下错误之一。 若要解决错误，请为每个错误条件使用所述步骤。


## <a name="failover-failed-with-error-id-28031"></a>故障转移失败，错误 ID 为 28031

Site Recovery 无法在 Azure 中创建故障转移的虚拟机。 以下其中一个原因也可能导致此情况的发生：

* 创建虚拟机的可用配额不足：你可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。

* 尝试在同一个可用性集中故障转移不同大小系列的虚拟机。 确保在同一个可用性集中选择相同大小系列的所有虚拟机。 可以转到虚拟机的“计算和网络”设置来更改大小，然后重试故障转移。

* 订阅上有一个阻止创建虚拟机的策略。 请更改此策略以允许创建虚拟机，然后重试故障转移。

## <a name="failover-failed-with-error-id-28092"></a>故障转移失败，错误 ID 为 28092

Site Recovery 无法为故障转移的虚拟机创建网络接口。 请确保订阅中有足够的配额来创建网络接口。 可以通过转到“订阅” -> “使用情况 + 配额”来检查可用配额。 可以打开 [新的支持请求](http://aka.ms/getazuresupport) 来增加此配额。 如果你拥有足够的配额，则这可能是一个间歇性的问题，请重试该操作。 如果即使在重试后问题仍然存在，请在本文档结尾处留下注释。  

## <a name="failover-failed-with-error-id-70038"></a>故障转移失败，错误 ID 为 70038

Site Recovery 无法在 Azure 中创建故障转移的经典虚拟机。 这可能是因为：

* 创建虚拟机所需的其中一个资源（如虚拟网络）不存在。 在虚拟机的“计算和网络”设置下创建虚拟网络，或者将设置修改为已经存在的虚拟网络，然后重试故障转移。


## <a name="next-steps"></a>后续步骤

如需更多帮助，请将疑问发布到 [Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)，或在本文档结尾处留下注释。 我们的活动社区应能够为你提供帮助。
