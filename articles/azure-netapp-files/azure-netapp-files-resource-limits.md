---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 介绍 Azure NetApp 文件资源的限制，包括容量池、卷和委派子网的限制。
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056272"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制
了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="capacity_pools"></a>容量池

- 单个容量池的最小大小为 4 TiB，最大大小为 500 TiB。 
- 每个容量池可以仅属于一个 NetApp 帐户。 但是，你可以在一个 NetApp 帐户中拥有多个容量池。  

## <a name="volumes"></a>卷

- 单个卷的最小大小为 100 GiB，最大大小为 92 TiB。
- 每个区域每个 Azure 订阅最多可以有 100 个卷。  

## <a name="delegated_subnet"></a>委派子网 

在每个 Azure 虚拟网络 (Vnet) 中，只能将一个子网委派给 Azure NetApp 文件。

## <a name="next-steps"></a>后续步骤

[了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
