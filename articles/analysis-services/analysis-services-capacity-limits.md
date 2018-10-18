---
title: Azure Analysis Services 资源和对象限制 | Microsoft Docs
description: 介绍 Azure Analysis Services 资源和对象限制。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4c2cebe2225e475ccd40460e7b10a6ba3ed428d5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723615"
---
# <a name="analysis-services-resource-and-object-limits"></a>Azure Analysis Services 资源和对象限制

本文介绍资源和模型对象限制。

## <a name="tier-limits"></a>层限制

### <a name="developer-tier"></a>开发人员层

对于评估、开发和测试方案，建议使用此层。 单个计划包含标准层的相同功能，但处理能力、QPU 和内存大小有限制。 查询副本横向扩展不适用于此层。 此层不提供 SLA。

|计划  |QPU  |内存 (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>基本层

建议在具有小型表格模型的生产解决方案、限制用户并发性和要求简单数据刷新的场合下使用该层。 查询副本横向扩展不适用于此层。 此层不支持透视图、多个分区和 DirectQuery 表格模型功能。  

|计划  |QPU  |内存 (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>标准层

此层适用于需要弹性用户并发性，且数据模型不断扩大的任务关键型生产应用程序。 它支持使用高级数据刷新实现接近实时的数据模型更新，并支持所有表格建模功能。

|计划  |QPU  |内存 (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* 并未在所有区域推出。  

## <a name="object-limits"></a>对象限制

以下是理论限制。 性能会随以下数值的降低而降低。

|对象|最大大小/数量|  
|------------|----------------------------|  
|实例中的数据库数|16,000|  
|数据库中表和列的总数|16,000|  
|表中的行数|不受限制<br /><br /> 警告：具有限制：表中单列的非重复值不能超过 1,999,999,997 个。|  
|表中的层次结构数|15,999|  
|层次结构中的级别数|15,999|  
|关系|8,000|  
|所有表中的键列数|15,999|  
|表中的度量值数|2^31-1 = 2,147,483,647|  
|查询返回的单元格数|2^31-1 = 2,147,483,647|  
|源查询的记录大小|64K|  
|对象名称的长度|512 个字符|  


