---
title: Azure Analysis Services 教程补充课程：详细信息行 | Microsoft Docs
description: 介绍了在 Azure Analysis Services 教程中如何创建详细信息行表达式。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 626258488afec4b3c3f025ae85bd3b5866aa0cf3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443971"
---
# <a name="supplemental-lesson---detail-rows"></a>补充课程 - 详细信息行

在本补充课程中，将使用 DAX 编辑器来定义自定义详细信息行表达式。 详细信息行表达式是关于度量值的一个属性，向最终用户提供关于度量值的聚合结果的详细信息。 
  
本课预计完成时间：**10 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本补充课程是表格建模教程的一部分。 在执行本补充课程中的任务之前，应当已完成了前面的所有课程，或者具有已完成的“Adventure Works Internet 销售”示例模型项目。  
  
## <a name="whats-the-issue"></a>问题出在哪里？
在添加详细信息行表达式之前，让我们看一下 InternetTotalSales 度量值的详细信息。

1.  在 SSDT 中，单击“模型”菜单 >“在 Excel 中分析”以打开 Excel 并创建一个空白数据透视表。
  
2.  在“数据透视表字段”中，将 FactInternetSales 表中的“InternetTotalSales”度量值添加到“值”，将 DimDate 表中的“CalendarYear”添加到“列”，将“EnglishCountryRegionName”添加到“行”。 数据透视表现在按区域和年份提供了 InternetTotalSales 度量值的聚合结果。 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. 在数据透视表中，双击针对某个年份和区域名称的聚合值。 双击针对澳大利亚和 2014 年的值。 此时将打开一个新的工作表，其中含有数据，但并无用处。

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
此处的目标是一个包含对 InternetTotalSales 度量值的聚合结果作出了贡献的数据列和数据行的表。 为实现此目标，可以将一个详细信息行表达式添加为该度量值的属性。

## <a name="add-a-detail-rows-expression"></a>添加详细信息行表达式

#### <a name="to-create-a-detail-rows-expression"></a>创建详细信息行表达式 
  
1. 在 FactInternetSales 表的度量值网格中，单击“InternetTotalSales”度量值。 

2. 在“属性” > “详细信息行表达式”中，单击编辑器按钮以打开 DAX 编辑器。

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. 在 DAX 编辑器中，输入以下表达式：

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    此表达式指定了当用户双击数据透视表或报表中的聚合结果时会从 FactInternetSales 表和相关表中返回的名称、列和度量值结果。

4. 回到 Excel 中，删除在第 3 步中创建的工作表，并双击某个聚合值。 此时，由于为该度量值定义了一个详细信息行表达式属性，因此，打开的新工作表包含许多更为有用的数据。

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. 重新部署模型。

  
## <a name="see-also"></a>另请参阅  

[SELECTCOLUMNS 函数 (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[补充课程 - 动态安全性](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[补充课程 - 不规则层次结构](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 