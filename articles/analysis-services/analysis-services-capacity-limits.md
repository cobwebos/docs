---
title: Azure Analysis Services 资源和对象限制 | Microsoft Docs
description: 介绍 Azure Analysis Services 资源和对象限制。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298703"
---
# <a name="analysis-services-resource-and-object-limits"></a>Azure Analysis Services 资源和对象限制

本文介绍资源和模型对象限制。

## <a name="tier-limits"></a>层限制

有关开发人员、基本层和标准层的 QPU 和内存限制，请参阅[Azure Analysis Services 定价页](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="object-limits"></a>对象限制

以下限制是理论限制。 性能会随以下数值的降低而降低。

|Object|最大大小/数量|  
|------------|----------------------------|  
|实例中的数据库数|16,000|  
|数据库中表和列的总数|16,000|  
|表中的行数|不受限制<br /><br /> **警告：** 具有限制：表中单列的非重复值不能超过 1,999,999,997 个。|  
|表中的层次结构数|15,999|  
|层次结构中的级别数|15,999|  
|关系|8,000|  
|所有表中的键列数|15,999|  
|表中的度量值|2^31-1 = 2,147,483,647|  
|查询返回的单元格数|2^31-1 = 2,147,483,647|  
|源查询的记录大小|64 K|  
|对象名称的长度|512 个字符|  


