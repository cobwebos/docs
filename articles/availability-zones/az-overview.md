---
title: "可用性区域概述 | Microsoft Docs"
description: "本文概述了 Azure 中的可用性区域。"
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c6225d368044ee2b2ab24136c854f81ae473c9e1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/23/2017

---

# <a name="overview-of-availability-zones-in-azure-preview"></a>Azure 中的可用性区域（预览版）概述

可用性区域可以在发生数据中心级故障时提供保护。 它们位于 Azure 地区内，并且每个都具有自己的独立电源、网络和冷却系统。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 在地区中以物理和逻辑方式分隔可用性区域可以在发生区域级故障的情况下保护应用程序和数据。 

![地区中发生故障的一个区域的概念视图](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>支持可用性区域的地区

- 美国东部 2
- 西欧

## <a name="services-that-support-availability-zones"></a>支持可用性区域的服务

支持可用性区域的 Azure 服务有：

- Linux 虚拟机
- Windows 虚拟机
- 地区性虚拟机规模集
- 托管磁盘
- 负载均衡器

## <a name="supported-virtual-machine-size-families"></a>支持的虚拟机大小系列

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>开始使用可用性区域预览版

可用性区域预览版目前在美国东部 2 和西欧地区中针对特定的 Azure 服务提供。 

1. [注册可用性区域预览版](http://aka.ms/azenroll)。 
2. 登录到 Azure 订阅。
3. 选择支持可用性区域的一个地区。
4. 使用下列链接之一开始将可用性区域用于你的服务。 
    - [创建虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)
    - [创建地区性虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [使用 PowerShell 添加托管磁盘](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [负载均衡器](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>后续步骤
- [快速入门模板](http://aka.ms/azqs)

