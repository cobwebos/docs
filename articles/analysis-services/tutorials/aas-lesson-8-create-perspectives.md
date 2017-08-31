---
title: "Azure Analysis Services 教程第 8 课：创建透视 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何创建透视。"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 491500909b0de0360afae45e172e85999d764fe0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-8-create-perspectives"></a>第 8 课：创建透视

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，你将创建“Internet 销售”透视。 透视定义模型的可查看子集，它提供集中的特定于业务的或特定于应用程序的视点。 当用户使用某个透视连接到模型时，他们只能看到在该透视中作为字段定义的那些模型对象（表、列、度量值、层次结构和 KPI）。 若要了解详细信息，请参阅[透视](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular)。
  
本课中创建的“Internet 销售”透视将排除 DimCustomer 表对象。 创建从视图中排除某对象的透视时，该对象仍然存在于模型中。 但是，它在报告客户端字段列表中不可见。 仍然可以基于排除的对象数据计算透视中包括或未包括的计算列和度量值。  
  
本课的目的是介绍如何创建透视并熟悉表格模型创作工具。 如果你之后扩展此模型来包括其他表，可以创建其他透视来定义模型的不同视点，例如“库存”和“销售”。  
  
本课预计完成时间：5 分钟  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，你应当已完成上一课：[第 7 课：创建关键绩效指标](../tutorials/aas-lesson-7-create-key-performance-indicators.md)。  
  
## <a name="create-perspectives"></a>创建透视  
  
#### <a name="to-create-an-internet-sales-perspective"></a>创建“Internet 销售”透视  
  
1.  单击“模型”菜单 >“透视” > “创建和管理”。  
  
2.  在“透视”对话框中，单击“新建透视”。  
  
3.  双击“新建透视”列标题，然后重命名“Internet 销售”。  
  
4.  选择除 DimCustomer 之外的所有表。  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    在后面的课程中，将使用“在 Excel 中分析”功能来测试此透视。 Excel 数据透视表字段列表包括除 DimCustomer 表之外的所有表。  

## <a name="whats-next"></a>后续步骤
[第 9 课：创建层次结构](../tutorials/aas-lesson-9-create-hierarchies.md)。
  
  
  
  

