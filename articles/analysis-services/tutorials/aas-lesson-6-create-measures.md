---
title: Azure Analysis Services 教程第 6 课：创建度量值 | Microsoft Docs
description: 介绍了在 Azure Analysis Services 教程项目中如何创建度量值。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31531abb5c2d2309317f1b88a1a794392b914f90
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-measures"></a>创建度量值

在本课中，将创建要在模型中包括的度量值。 与所创建的计算列类似，度量值是使用 DAX 公式创建的计算。 但与计算列不同，度量值基于用户所选筛选器进行计算。 例如，添加到数据透视表中“行标签”字段的特定一列或切片器。 然后，所应用的度量值将计算筛选器中每个单元格的值。 度量值是强大而灵活的计算，在几乎所有表格模型中都希望包括这类计算来对数值数据执行动态计算。 若要了解详细信息，请参阅[度量值](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular)。
  
若要创建度量值，需要使用度量值网格。 默认情况下，每个表都有一个空的度量值网格；不过，通常不会为每个表都创建度量值。 在模型设计器的“数据视图”中，度量值网格显示在表下方。 要隐藏或显示表的度量值网格，请单击“表”菜单，并单击“显示度量值网格”。  
  
可以通过以下方式创建度量值：单击度量值网格中的某个空单元格，然后在公式栏中键入一个 DAX 公式。 当单击 Enter 以完成公式时，度量值就显示在单元格中。 还可以通过以下方式使用标准聚合函数创建度量值：单击某个列，然后单击工具栏上的“自动求和”按钮 (**∑**)。 使用“自动求和”功能创建的度量值显示在该列正下方的度量值网格单元格中，但可以移动。  
  
在本课中，通过在公式栏中输入 DAX 公式和使用“自动求和”功能这两种方式创建度量值。  
  
本课程预计完成时间：**30 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，应当已完成上一课：[第 5 课：创建计算列](../tutorials/aas-lesson-5-create-calculated-columns.md)。  
  
## <a name="create-measures"></a>创建度量值  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>在 DimDate 表中创建 DaysCurrentQuarterToDate 度量值  
  
1.  在模型设计器中，单击“DimDate”表。  
  
2.  在度量值网格中，单击左上角的空单元格。  
  
3.  在公式栏中，键入以下公式：  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    请注意，左上角的单元格现在包含度量值名称“DaysCurrentQuarterToDate”，后面跟有结果 **92**。 此时的结果不相关，因为尚未应用用户筛选器。
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    与计算列不同，使用度量值公式时，可以键入度量值名称，然后键入一个冒号，再键入公式表达式。

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>在 DimDate 表中创建 DaysInCurrentQuarter 度量值  
  
1.  “DimDate”表仍然在模型设计器中处于活动状态时，在度量值网格中，单击创建的度量值下方的空单元格。  
  
2.  在公式栏中，键入以下公式：  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    在不完整时段和前一时段之间创建一个对比比率时， 该公式必须计算已过时段的比例，将其与前一时段中相同的比例进行比较。 在本例中，[DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] 给出了当前期间中已过去时间的比例。  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>在 FactInternetSales 表中创建 InternetDistinctCountSalesOrder 度量值  
  
1.  单击“FactInternetSales”表。   
  
2.  单击“SalesOrderNumber”列标题。  
  
3.  在工具栏上，单击“自动求和”按钮 (**∑**) 旁边的向下箭头，并选择“DistinctCountDistinctCount”。  
  
    “自动求和”功能会自动使用 DistinctCount 标准聚合公式为所选的列创建一个度量值。  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  在度量值网格中，单击新的度量值，然后在“属性”窗口中，在“度量值名称”中将度量值重命名为“InternetDistinctCountSalesOrder”。 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>在 FactInternetSales 表中创建其他度量值  
  
1.  使用“自动求和”功能创建并命名以下度量值：  

    |列|度量值名称|自动求和 (∑)|公式|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Count|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Sum|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Sum|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Sum|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Sum|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Sum|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Sum|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Sum|=SUM([Freight])|  
  
2.  通过单击度量值网格中的某个空单元格并使用公式栏，按顺序创建以下自定义度量值：  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
为 FactInternetSales 表创建的度量值可以用来为由用户选择的筛选器定义的项分析关键财务数据，例如销售额、成本和利润率。  
  
## <a name="whats-next"></a>后续步骤
[第 7 课：创建关键绩效指标](../tutorials/aas-lesson-7-create-key-performance-indicators.md)。  

  
