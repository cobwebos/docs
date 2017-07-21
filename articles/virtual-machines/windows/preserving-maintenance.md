---
title: "Azure 中 Windows VM 的 VM 保留维护 | Microsoft Docs"
description: "用于内存保留更新的就地 VM 迁移。"
services: virtual-machines-windows
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 8888bafbc3aba24168312b611a9b4fbde25f376d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---



# <a name="vm-preserving-maintenance-with-in-place-vm-migration"></a>使用就地 VM 迁移的 VM 保留维护

尽管大多数更新对托管的 VM 无任何影响，但在某些情况下，组件或服务更新会对运行的 VM（未完全重启虚拟机）产生一些干扰。

这些更新通过启用就地实时迁移技术（也称为“内存保留更新”）实现。 更新主机时，虚拟机进入“暂停”状态，以保留 RAM 中的内存，而托管环境（例如基础操作系统）则应用必要的更新和补丁。
虚拟机在暂停后 30 秒内恢复正常。
恢复后，虚拟机的时钟将自动同步。

并非所有更新都可以通过使用此机制进行部署，但如果短暂时间较短，使用此方法部署更新可大大减少对虚拟机的影响。

多实例更新（针对可用性集中的虚拟机）一次应用一个更新域。

这些更新对某些应用程序的影响可能比对其他应用程序的影响大。 例如，执行实时事件处理、媒体流或转码或高吞吐量网络方案的应用程序可能无法容忍暂停 30 秒钟。 通过调用 [Azure 元数据服务](../virtual-machines-instancemetadataservice-overview.md)的[计划事件](../virtual-machines-scheduled-events.md) API，虚拟机中运行的应用程序可获知即将进行的更新。

