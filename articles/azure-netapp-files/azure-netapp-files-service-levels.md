---
title: Azure NetApp 文件的服务级别 | Microsoft Docs
description: 介绍 Azure NetApp 文件的服务级别的吞吐量性能。
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
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668338"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
Azure NetApp 文件支持两个服务级别：“高级”和“标准”。 

## <a name="Premium"></a>高级存储

*高级*存储提供每 TiB 高达 64 MiB/s 的吞吐量。 根据卷配额对吞吐量性能编制索引。 例如，来自高级存储的拥有 2 TiB 预配配额（不考虑实际用量）的卷具有 128 MiB/s 的吞吐量。

## <a name="Standard"></a>标准存储

*标准*存储提供每 TiB 高达 16 MiB/s 的吞吐量。 根据卷配额对吞吐量性能编制索引。 例如，来自标准存储的拥有 2 TiB 预配配额（不考虑实际用量）的卷具有 32 MiB/s 的吞吐量。

## <a name="next-steps"></a>后续步骤

- 有关不同服务级别的价格，请参阅 [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
