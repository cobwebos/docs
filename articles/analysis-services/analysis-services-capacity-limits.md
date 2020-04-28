---
title: Azure Analysis Services 资源和对象限制 | Microsoft Docs
description: 本文介绍 Azure Analysis Services 服务器的资源和对象限制。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "73573200"
---
# <a name="analysis-services-resource-and-object-limits"></a>Azure Analysis Services 资源和对象限制

本文介绍资源和模型对象限制。

## <a name="tier-limits"></a>层限制

有关开发人员、基本层和标准层的 QPU 和内存限制，请参阅[Azure Analysis Services 定价页](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="object-limits"></a>对象限制

以下限制是理论限制。 性能会随以下数值的降低而降低。

|Object|最大大小/最大数量|  
|------------|----------------------------|  
|实例中的数据库|16,000|  
|数据库中表和列的总数|16,000|  
|表中的行|无限制<br /><br /> 警告：具有限制：表中单列的非重复值不能超过 1,999,999,997 个  。|  
|表中的层次结构|15,999|  
|层次结构中的级别数|15,999|  
|关系|8,000|  
|所有表中的键列数|15,999|  
|表中的度量值|2^31-1 = 2,147,483,647|  
|查询返回的单元|2^31-1 = 2,147,483,647|  
|源查询的记录大小|64 K|  
|对象名的长度|512 个字符|  


