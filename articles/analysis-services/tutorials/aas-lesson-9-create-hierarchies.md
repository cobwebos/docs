---
title: "Azure Analysis Services 教程第 9 课：创建层次结构 | Microsoft Docs"
description: 
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 28e4e24f5706e88ede25060d5459617befd4aea9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-9-create-hierarchies"></a>第 9 课：创建层次结构

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，将创建层次结构。 层次结构是一组组的列，按级别排列。 例如，“地理区域”层次结构可包含“国家/地区”、“省/市/自治区”、“县”和“城市”子级别。 层次结构在报告客户端应用程序字段列表中可以与其他列分开显示，这使得客户端用户更容易在其中导航以及将其包括在报表中。 若要了解详细信息，请参阅[层次结构](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
若要创建层次结构，请在“关系图视图”中使用模型设计器。 “数据视图”中不支持创建和管理层次结构。  
  
本课预计完成时间：**20 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 8 课：创建透视](../tutorials/aas-lesson-8-create-perspectives.md)。  
  
## <a name="create-hierarchies"></a>创建层次结构  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>在 DimProduct 表中创建 Category 层次结构  
  
1.  在模型设计器（关系图视图）中，右键单击“DimProduct”表，并单击“创建层次结构”。 一个新的层次结构将出现在表窗口的底部。 将该层次结构重命名为“Category”。  
  
2.  单击“ProductCategoryName”列并将其拖动到新的“Category”层次结构。  
  
3.  在“Category”层次结构中，右键单击“ProductCategoryName”，再单击“重命名”，然后键入“Category”。 >   
  
    > [!NOTE]  
    > 重命名层次结构中的某个列不会重命名表中的该列。 层次结构中的列只是表中的该列的一种表示形式。  
  
4.  单击“ProductSubcategoryName”列并将其拖动到新的“Category”层次结构。 将其重命名为“Subcategory”。 
  
5.  右键单击“ModelName”列，再单击“添加到层次结构”，然后选择“Category”。 将其重命名为“Model”。

6.  最后，将“EnglishProductName”添加到 Category 层次结构。 将其重命名为“Product”。  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>在 DimDate 表中创建层次结构  
  
1.  在 DimDate 表中，创建一个名为“Calendar”的层次结构。  
  
3.  按顺序添加以下列：

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  在“DimDate”表中，创建“Fiscal”层次结构。 按顺序包括以下列：  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  最后，在“DimDate”表中，创建“ProductionCalendar”层次结构。 按顺序包括以下列：  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>后续步骤
[第 10 课：创建分区](../tutorials/aas-lesson-10-create-partitions.md)。 
  
  
