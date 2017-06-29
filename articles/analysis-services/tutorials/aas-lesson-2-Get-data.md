---
title: "Azure Analysis Services 教程第 2 课：获取数据 | Microsoft Docs"
description: "介绍了在 Azure Analysis Services 教程项目中如何获取和导入数据。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: e77de4b9a74b528fa8a7ce86424fc14628b2cacc
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017

---

# <a name="lesson-2-get-data"></a>第 2 课：获取数据

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

在本课中，可以使用 SSDT 中的“获取数据”功能来连接到 AdventureWorksDW2014 示例数据库，选择数据，预览并筛选，然后将数据导入到你的模型工作区。  
  
使用“获取数据”，你可以从各种源导入数据：Azure SQL 数据库、Oracle、Sybase、OData Feed、Teradata，文件和其他源。 还可以使用 Power Query M 公式表达式查询数据。
  
本课预计完成时间：**10 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本主题是表格建模教程的一部分，应当按顺序完成。 在执行本课中的任务之前，你应当已完成上一课：[第 1 课：创建新的表格模型项目](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)。  
  
## <a name="create-a-connection"></a>创建连接  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>创建到 AdventureWorksDW2014 数据库的连接  
  
1.  在表格模型资源管理器中，右键单击“数据源”，然后单击“从数据源导入”。 >   
  
    这将启动“获取数据”，它将指导你连接到数据源。 如果没有看到表格模型资源管理器，请在“解决方案资源管理器”中双击“Model.bim”以在设计器中打开该模型。 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  在“获取数据”中，单击“数据库” > “SQL Server 数据库” > “连接”。  
  
3.  在“SQL Server 数据库”对话框中，在“服务器”中键入你在其中安装了 AdventureWorksDW2014 数据库的服务器的名称，然后单击“连接”。  

4.  当提示输入凭据时，你需要指定 Analysis Services 在导入和处理数据时用来连接到数据源的凭据。 在“模拟模式”中，选择“模拟帐户”，然后输入凭据并单击“连接”。 建议使用密码没有过期的帐户。

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > 使用 Windows 用户帐户和密码可以提供连接到数据源的最安全方法。
  
5.  在导航器中，选择“AdventureWorksDW2014”数据库，然后单击“确定”。这将创建到该数据库的连接。 
  
6.  在导航器中，选中下列表的复选框：“DimCustomer”、“DimDate”、“DimGeography”、“DimProduct”、“DimProductCategory”、“DimProductSubcategory”和“FactInternetSales”。  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
单击“确定”后，将打开“查询编辑器”。 在下一部分中，仅选择要导入的数据。

  
## <a name="filter-the-table-data"></a>筛选表数据  
AdventureWorksDW2014 示例数据库中的表具有不需要包括在模型中的数据。 如果可能，可筛选掉不必要的数据，节省模型使用的内存中空间。 筛选掉表中的一些列，以便在部署工作区数据库或模型数据库后不会将这些列导入到该数据库中。 
  
#### <a name="to-filter-the-table-data-before-importing"></a>在导入之前筛选表数据  
  
1.  在查询编辑器中，选择“DimCustomer”表。 此时将显示数据源（AdventureWorksDWQ2014 示例数据库）中的 DimCustomer 表的一个视图。 
  
2.  以多选方式（在按住 Ctrl 的情况下单击）选择 **SpanishEducation**、**FrenchEducation**、**SpanishOccupation**、**FrenchOccupation**，然后单击右键并单击“删除列”。 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    因为这些列的值与 Internet 销售分析无关，因此不需要导入这些列。 消除不需要的列可以使模型更小更高效。  
  
4.  对剩余的表进行筛选，删除每个表中的以下列：  
    
    **DimDate**
    
      |列|  
      |--------|  
      |日期键|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |列|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |列|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |列|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |列|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      |列|  
      |------------------|  
      |**OrderDateKey**|  
      |**DueDateKey**|  
      |**ShipDateKey**|   
  
## <a name="Import"></a>导入所选的表和列数据  
现在，你已预览并筛选掉了不需要的数据，可以导入你确实需要的剩余数据了。 向导将导入表数据以及各个表之间的关系。 将在模型中创建新的表和列，并且不会导入已筛选掉的数据。  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>导入所选的表和列数据  
  
1.  复查你的选择。 如果一切看起来都没有问题，请单击“导入”。 “数据处理”对话框将显示正在从数据源导入到工作区数据库的数据的状态。
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  单击“**关闭**”。  

  
## <a name="save-your-model-project"></a>保存模型项目  
频繁保存模型项目非常重要。  
  
#### <a name="to-save-the-model-project"></a>保存模型项目  
  
-   单击“文件” > “全部保存”。  
  
## <a name="whats-next"></a>后续步骤
[第 3 课：标记为日期表](../tutorials/aas-lesson-3-mark-as-date-table.md)。

  
  

