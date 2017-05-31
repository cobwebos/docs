---
title: "Azure 中的 Windows VM 的计划内维护 | Microsoft Docs"
description: "了解什么是 Azure 计划内维护以及它如何影响正在 Azure 中运行的 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Windows 虚拟机的计划内维护 

Microsoft Azure 在全球范围内定期执行更新，以提高虚拟机所基于的主机基础结构的可靠性、性能及安全性。 此类更新包括修补主机环境（OS、虚拟机监控程序以及主机上部署的各种代理）中的软件组件、升级网络组件以及硬件解除授权等多项内容。

大多数此类更新在执行时不会影响托管的虚拟机或云服务。

但是，也会存在更新对托管的虚拟机产生影响的情况：

-   使用就地 VM 迁移的 VM 保留维护会描述一种类型的更新，其中虚拟机在维护期间不会被重启。

-   VM 重启维护，维护时需要重启或重新部署到托管虚拟机。

请注意，本页介绍 Microsoft Azure 如何执行计划内维护。 有关非计划事件（故障）的详细信息，请参阅[管理虚拟机的可用性](manage-availability.md)。
