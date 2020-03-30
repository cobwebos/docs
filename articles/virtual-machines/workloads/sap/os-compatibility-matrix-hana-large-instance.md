---
title: SAP HANA（大型实例）的操作系统兼容性矩阵*微软文档
description: 兼容性矩阵表示不同版本的操作系统与不同硬件类型（大型实例）的兼容性
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675644"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>适用于 HANA 大型实例的兼容操作系统

## <a name="hana-large-instance-type-i"></a>HANA 大型实例类型 I     
  | 操作系统 | 可用性        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 不再提供 | S72， S72m， S96， S144， S144m， S192， S192m， S192xm              |
  | SLES 12 SP3      | 可用           | S72， S72m， S96， S144， S144m， S192， S192m， S192xm              |
  | SLES 12 SP4      | 可用           | S72， S72m， S96， S144， S144m， S192， S192m， S192xm， S224， S224m |
  
### <a name="persistent-memory-skus"></a>持久内存 SKU
  | 操作系统 | 可用性 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 可用    | S224oo， S224om， S224oo， S224oo |
  
## <a name="hana-large-instance-type-ii"></a>HANA 大型实例类型 II     
  |  操作系统       | 可用性        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | 不再提供 | S384， S384m， S384xm， S384xxm， S576m， S576xm， S768m， S768xm， S960m |
  | SLES 12 SP3             | 可用           | S384， S384m， S384xm， S384xxm， S576m， S576xm， S768m， S768xm， S960m |
  
## <a name="related-documents"></a>相关文档

- 了解有关[可用 SKU](hana-available-skus.md)的更多资料
- 了解有关[升级操作系统](os-upgrade-hana-large-instance.md)的介绍
  

  
  
