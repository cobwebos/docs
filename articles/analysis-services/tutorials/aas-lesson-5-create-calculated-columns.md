---
title: "Azure Analysis Services 教程第 5 课：创建计算列 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何创建计算列。"
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
ms.openlocfilehash: d7d2265aa82e54cdbd3b85e09a05e5ea55a7a2c2
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="lesson-5-create-calculated-columns"></a>第 5 课：创建计算列

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，将通过添加计算列在模型中创建数据。 可以在使用“获取数据”时（通过使用查询编辑器）或稍后在模型设计器中（按照本教程中介绍的步骤）创建计算列（作为自定义列）。 若要了解详细信息，请参阅[计算列](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns)。
  
将在三个不同的表中创建五个新的计算列。 每个任务的执行步骤稍有不同，说明可通过多种方法创建列、对其进行重命名，以及将其放置在表中的各种位置。  

还将在本课中首次使用数据分析表达式 (DAX)。 DAX 是一种特殊语言，用于为表格模型创建可高度自定义的公式表达式。 在本教程中，将使用 DAX 创建计算列、度量值和角色筛选器。 若要了解详细信息，请参阅[表格模型中的 DAX](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular)。 
  
本课预计完成时间：**15 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 4 课：创建关系](../tutorials/aas-lesson-4-create-relationships.md)。 
  
## <a name="create-calculated-columns"></a>创建计算列  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>在 DimDate 表中创建 MonthCalendar 计算列  
  
1.  单击“模型”菜单 >“模型视图” > “视图”。  
  
    只能使用模型设计器在“数据视图”中创建计算列。  
  
2.  在模型设计器中，单击“DimDate”表（选项卡）。  
  
3.  右键单击“CalendarQuarter”列标题，并单击“插入列”。  
  
    一个名为“Calculated Column 1”的新列将插入到“Calendar Quarter”列的左侧。  
  
4.  在表上方的编辑栏中，键入以下 DAX 公式：“自动完成”，这可帮助键入列和表的完全限定名称，并列出可用函数。  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    然后，会在计算列中为所有行填充值。 如果在表中向下滚动，可看到，根据每行中的数据，各行中的此列可能具有不同的值。    
  
5.  将此列重命名为“MonthCalendar”。 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
MonthCalendar 计算列提供月份的可排序名称。  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>在 DimDate 表中创建 DayOfWeek 计算列  
  
1.  在 DimDate 表仍然处于活动状态的情况下，单击“列”菜单，然后单击“添加列”。  
  
2.  在公式栏中，键入以下公式：  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    完成公式构建后，按 ENTER。 新列将添加到表的最右侧。  
  
3.  将此列重命名为“DayOfWeek”。  
  
4.  单击列标题，然后将该列拖动到 EnglishDayNameOfWeek 列和 DayNumberOfMonth 列之间。  
  
    > [!TIP]  
    > 移动表中的列可使其更容易导航。  
  
DayOfWeek 计算列提供周几的可排序名称。  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>在 DimProduct 表中创建 ProductSubcategoryName 计算列  
  
  
1.  在“DimProduct”表中，滚动到表的最右侧。 请注意，最右侧的列名为“添加列”（斜体），单击列标题。  
  
2.  在公式栏中，键入以下公式：  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  将该列重命名为“ProductSubcategoryName”。  
  
ProductSubcategoryName 计算列用于在 DimProduct 表中创建层次结构，该层次结构将包括 DimProductSubcategory 表的 EnglishProductSubcategoryName 列中的数据。 层次结构不能跨多个表。 稍后将在第 9 课中创建层次结构。  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>在 DimProduct 表中创建 ProductCategoryName 计算列  
  
1.  在“DimProduct”表仍然处于活动状态的情况下，单击“列”菜单，并单击“添加列”。  
  
2.  在公式栏中，键入以下公式：  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  将该列重命名为“ProductCategoryName”。  
  
ProductCategoryName 计算列用于在 DimProduct 表中创建层次结构，该层次结构包括 DimProductCategory 表的 EnglishProductCategoryName 列中的数据。 层次结构不能跨多个表。  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>在 FactInternetSales 表中创建 Margin 计算列  
  
1.  在模型设计器中，选择“FactInternetSales”表。  
  
2.  在“SalesAmount”列与“TaxAmt”列之间创建一个新的计算列。  
  
3.  在公式栏中，键入以下公式：  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  将此列重命名为“Margin”。  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Margin 计算列用来分析每次销售的利润率。  
  
## <a name="whats-next"></a>后续步骤
[第 6 课：创建度量值](../tutorials/aas-lesson-6-create-measures.md)。
  
  
  
