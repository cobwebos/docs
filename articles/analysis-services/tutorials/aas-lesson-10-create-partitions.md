---
title: "Azure Analysis Services 教程第 10 课：创建分区 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何创建分区。"
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
ms.openlocfilehash: df74d9cbdcf4916c24955e491767589e72389155
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-10-create-partitions"></a>第 10 课：创建分区

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，你将创建分区来将 FactInternetSales 表拆分为较小的逻辑部件，然后可以彼此独立地处理（刷新）各个逻辑部件。 默认情况下，模型中包含的每个表都有一个分区，该分区包括该表的所有列和行。 对于 FactInternetSales 表，我们希望按年份拆分数据；每个分区包含表中每五年的数据。 可以独立处理每个分区。 若要了解详细信息，请参阅[分区](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular)。 
  
本课预计完成时间：**15 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，你应当已完成上一课：[第 9 课：创建层次结构](../tutorials/aas-lesson-9-create-hierarchies.md)。  
  
## <a name="create-partitions"></a>创建分区  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>若要在 FactInternetSales 表中创建分区  
  
1.  在表格建模资源管理器中，展开“表”，然后右键单击“FactInternetSales”，再单击“分区”。 >   
  
2.  在分区管理器中，单击“复制”，然后将名称更改为“FactInternetSales2010”。
  
    因为你希望分区仅包括特定期间内的那些行，因此，对于 2010 年，必须修改查询表达式。
  
4.  单击“设计”以打开查询编辑器，然后单击“FactInternetSales2010”查询。

5.  在预览中，单击 **OrderDate** 列标题中的向下箭头，然后单击“日期/时间筛选器” > “介于”。

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  在“筛选行”对话框中，在“显示符合以下条件的行: OrderDate”中，保留“晚于或等于”，然后在日期字段中输入 **1/1/2010**。 将“且”运算符保留为选中状态，选择“早于”，然后在日期字段中输入 **1/1/2011**，然后单击“确定”。

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    请注意，在“查询编辑器”中的“APPLIED STEPS”中，将看到另一个名为“筛选的行”的步骤。 此筛选器仅选择 2010 年的订单日期。

8.  单击“导入”。

    在分区管理器中，请注意，查询表达式现在具有一个附加的 Filtered Rows 子句。

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    此语句指定此分区应当仅包括其中的 OrderDate 在 2010 日历年的那些行中的数据。  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>为 2011 年创建分区  
  
1.  在分区列表中，单击你创建的“FactInternetSales2010”分区，然后单击“复制”。  将分区名称更改为“FactInternetSales2011”。 

    不需要使用查询编辑器来创建新的 filtered rows 子句。 因为你已经创建了针对 2010 年的查询的副本，你需要做的所有事情就是针对 2011 年对该查询做细微更改。
  
2.  在**查询表达式**中，为了使此分区仅包括 2011 年的那些行，请将 Filtered Rows 子句中的年份分别替换为 **2011** 和 **2012**，如下所示：  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>为 2012、2013 和 2014 年创建分区  
  
- 按照前面的步骤为 2012、2013 和 2014 年创建分区，更改 Filtered Rows 子句中的年份以便仅包括该年份的行。 
  

## <a name="delete-the-factinternetsales-partition"></a>删除 FactInternetSales 分区
现在，你已有了用于每个年份的分区，你可以删除 FactInternetSales 分区；这可以防止在处理分区时选择了“全部处理”时出现重叠。

#### <a name="to-delete-the-factinternetsales-partition"></a>删除 FactInternetSales 分区
-  单击 FactInternetSales 分区，然后单击“删除”。



## <a name="process-partitions"></a>处理分区  
在分区管理器中，请注意，你创建的每个新分区的“上次处理时间”列表明这些分区从未处理过。 在创建分区时，你应当运行“处理分区”或“处理表”操作来刷新那些分区中的数据。  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>处理 FactInternetSales 分区  
  
1.  单击“确定”以关闭分区管理器。  
  
2.  单击“FactInternetSales”表，然后单击“模型”菜单 >“处理” > “处理分区”。  
  
3.  在“处理分区”对话框中，验证“模式”是否已设置为“处理默认值”。  
  
4.  选中你创建的五个分区中每个分区的“处理”列中的复选框，然后单击“确定”。  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    如果提示你输入模拟凭据，请输入你在第 2 课中指定的 Windows 用户名和密码。  
  
    此时将出现“数据处理”对话框，其中显示了每个分区的处理详细信息。 请注意，为每个分区传输的行数不同。 每个分区仅包括 SQL 语句的 WHERE 子句指定年份的那些行。 当处理完成后，继续操作并关闭“数据处理”对话框。  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>后续步骤
转到下一课：[第 11 课：创建角色](../tutorials/aas-lesson-11-create-roles.md)。 

