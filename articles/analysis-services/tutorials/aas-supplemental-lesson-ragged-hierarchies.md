---
title: "Azure Analysis Services 教程补充课程：不规则层次结构 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程中如何修复不规则层次结构。"
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
ms.date: 10/16/2017
ms.author: owend
ms.openlocfilehash: 74058b34dc38172f8e0daabfac0e847df71ed553
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2017
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>补充课程 - 不规则层次结构

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本补充课程中，将解决对在不同级别包含空白值（成员）的层次结构进行透视时的一个常见问题。 例如，某个组织的高级经理同时具有部门经理和非经理作为直接下属。 或者，地理区域层次结构包含国家/地区-区域-城市，其中，某些城市没有父代“州”或“省”，例如“华盛顿特区”、“梵蒂冈城”。 当某个层次结构具有空白成员时，它通常降低到不同的（或不规则的）级别。

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

1400 兼容级别的表格模型有一个额外的针对层次结构的“隐藏成员”属性。 **默认**设置假定在任何级别都没有空白成员。 将层次结构添加到数据透视表或报表时，“隐藏空白成员”设置将从层次结构中排除空白成员。  
  
本课预计完成时间：**20 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本补充课程主题是表格建模教程的一部分。 在执行本补充课程中的任务之前，应当已完成了前面的所有课程，或者具有已完成的“Adventure Works Internet 销售”示例模型项目。 

如果已经将“AW Internet 销售”项目创建为教程的一部分，则模型中尚未包含任何不规则的数据或层次结构。 若要完成本补充课程，必须首先通过添加一些其他表并创建关系、计算列、度量值和新的 Organization 层次结构来制造问题。 该部分需要花费大约 15 分钟。 然后，需要花费几分钟时间来解决此问题。  

## <a name="add-tables-and-objects"></a>添加表和对象
  
### <a name="to-add-new-tables-to-your-model"></a>向模型中添加新表
  
1.  在表格模型资源管理器中，展开“数据源”，然后右键单击连接，再单击“导入新表”。
  
2.  在导航器中，选择“DimEmployee”和“FactResellerSales”，并单击“确定”。

3.  在查询编辑器中，单击“导入”。

4.  创建以下[关系](../tutorials/aas-lesson-4-create-relationships.md)：

    | 表 1           | 列       | 筛选器方向   | 表 2     | 列      | 活动 |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | 默认            | DimDate     | Date        | 是    |
    | FactResellerSales | DueDate      | 默认            | DimDate     | 日期        | 否     |
    | FactResellerSales | ShipDateKey  | 默认            | DimDate     | 日期        | 否     |
    | FactResellerSales | ProductKey   | 默认            | DimProduct  | ProductKey  | 是    |
    | FactResellerSales | EmployeeKey  | 到两个表 | DimEmployee | EmployeeKey | 是    |

5. 在“DimEmployee”表中，创建以下[计算列](../tutorials/aas-lesson-5-create-calculated-columns.md)： 

    **Path** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  在“DimEmployee”表中，创建一个名为“Organization”的[层次结构](../tutorials/aas-lesson-9-create-hierarchies.md)。 按顺序添加以下列：“Level1”、“Level2”、“Level3”、“Level4”、“Level5”。

7.  在“FactResellerSales”表中，创建以下[度量值](../tutorials/aas-lesson-6-create-measures.md)：

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  使用[“在 Excel 中分析”](../tutorials/aas-lesson-12-analyze-in-excel.md)打开 Excel 并自动创建一个数据透视表。

9.  在“数据透视表字段”中，将“DimEmployee”表中的“Organization”层次结构添加到“行”，将“FactResellerSales”表中的“ResellerTotalSales”度量值添加到“值”。

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    如数据透视表中所示，该层次结构显示了不规则的行。 许多行中显示了空白成员。

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>通过设置“隐藏成员”属性修复不规则层次结构

1.  在“表格模型资源管理器”中，展开“表” > “DimEmployee” > “层次结构” > “Organization”。

2.  在“属性” > “隐藏成员”中，选择“隐藏空白成员”。 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  回到 Excel 中，刷新数据透视表。 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    现在看起来好多了！

## <a name="see-also"></a>另请参阅   
[第 9 课：创建层次结构](../tutorials/aas-lesson-9-create-hierarchies.md)  
[补充课程 - 动态安全性](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[补充课程 - 详细信息行](../tutorials/aas-supplemental-lesson-detail-rows.md)  
