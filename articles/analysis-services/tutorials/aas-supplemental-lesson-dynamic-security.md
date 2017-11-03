---
title: "Azure Analysis Services 教程补充课程：动态安全性 | Microsoft Docs"
description: "介绍如何通过 Azure Analysis Services 教程中的行筛选器来使用动态安全性。"
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
ms.openlocfilehash: 919094c5e8c528810ce6545d6b0cf8d9f95cca2a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="supplemental-lesson---dynamic-security"></a>补充课程 - 动态安全性

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

本补充课程将创建一个附加的角色用于实现动态安全性。 动态安全性基于当前已登录用户的用户名或登录 ID 提供行级别安全性。 
  
要实现动态安全性，需要将一个表添加到模型，该表包含用户名以及可连接到模型和浏览模型对象与数据的用户。 使用本教程创建的模型在 Adventure Works 的上下文中；但是，要完成本课程，必须从自己的域添加一个包含用户的表。 不需要已添加的用户名的密码。 要使用来自自己域中的少量用户示例创建 EmployeeSecurity 表，可以使用“粘贴”功能粘贴 Excel 电子表格中的员工数据。 在实际方案中，包含用户名的表通常是用作数据源的数据库中的某个表；例如，实际的 DimEmployee 表。  
  
若要实现动态安全性，可以使用两个 DAX 函数：[USERNAME 函数 (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) 和 [LOOKUPVALUE 函数 (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab)。 在行筛选器公式中应用的这些函数在新角色中定义。 该公式通过使用 LOOKUPVALUE 函数，指定 EmployeeSecurity 表中的值。 公式随后将该值传递给 USERNAME 函数，该函数指定登录用户的用户名属于此角色。 然后，用户只能浏览角色的行筛选器指定的数据。 在此方案中，将指定销售员工只能浏览他们所属销售区域的 Internet 销售数据。  
  
本 Adventure Works 表格模型方案特有的、但不一定适用于真实方案的任务就是以这种形式确定的。 每个任务包括附加的信息用于描述任务的目的。  
  
本课程预计完成时间：**30 分钟**  
  
## <a name="prerequisites"></a>先决条件  
本补充课程主题是表格建模教程的一部分，应当按顺序完成。 在执行本补充课程中的任务之前，应已完成前面的课程。  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>将 DimSalesTerritory 表添加到 AW Internet 销售表格模型项目  
若要为此 Adventure Works 方案实现动态安全性，必须将另外两个表添加到模型。 添加的第一个表是来自同一 AdventureWorksDW 数据库的 DimSalesTerritory（即销售区域）。 稍后要向 SalesTerritory 表应用行筛选器，用于定义已登录用户可以浏览的特定数据。  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>添加 DimSalesTerritory 表  
  
1.  在表格模型资源管理器中，转到“数据源”，右键单击连接，并单击“导入新表”。  

    如果出现“模拟凭据”对话框，请键入在“第 2 课：添加数据”中使用的模拟凭据。
  
2.  在导航器中选择“DimSalesTerritory”表，并单击“确定”。    
  
3.  在查询编辑器中单击“DimSalesTerritory”查询，并删除“SalesTerritoryAlternateKey”列。  
  
7.  单击“导入”。  
  
    新表随即添加到模型工作区。 然后，DimSalesTerritory 源表中的对象和数据将导入 AW Internet 销售表格模型。  
  
9. 成功导入表后，请单击“关闭”。  

## <a name="add-a-table-with-user-name-data"></a>添加包含用户名数据的表  
AdventureWorksDW 示例数据库中的 DimEmployee 表包含 AdventureWorks 域中的用户。 自己的环境中不存在这些用户名。 必须在模型中创建一个表，该表需包含用户组织中实际用户的小型样本（至少三个）。 然后，将这些用户作为成员添加到新角色。 不需要示例用户名的密码，但需要来自自己域的实际 Windows 用户名。  
  
#### <a name="to-add-an-employeesecurity-table"></a>添加 EmployeeSecurity 表  
  
1.  打开 Microsoft Excel 并创建工作表。  
  
2.  复制以下表（包括标题行），然后将其粘贴到该工作表。  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  将名字、姓氏和域\用户名替换为组织中三个用户的姓名和登录 ID。 对于 EmployeeId 1，请将同一用户放在前两行，表示此用户属于多个销售区域。 将 EmployeeId 和 SalesTerritoryId 字段保留原样。  
  
4.  将该工作表保存为 **SampleEmployee**。  
  
5.  在工作表中，选择包含员工数据的所有单元格（包括标题），然后右键单击所选数据并单击“复制”。  
  
6.  在 SSDT 中单击“编辑”菜单，并单击“粘贴”。  
  
    如果“粘贴”灰显，请在模型设计器窗口中单击任一表中的任一列，并重试。  
  
7.  在“粘贴预览”对话框中的“表名”内，键入 **EmployeeSecurity**。  
  
8.  在“要粘贴的数据”中，检查数据是否包括 SampleEmployee 表中的所有用户数据和标题。  
  
9. 检查是否已选中“使用第一行作为列标题”，并单击“确定”。  
  
    现已创建名为 EmployeeSecurity 的新表，其中包含从 SampleEmployee 工作表复制的员工数据。  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>在 FactInternetSales、DimGeography 和 DimSalesTerritory 表之间创建关系  
所有 FactInternetSales、DimGeography 和 DimSalesTerritory 表包含通用列 SalesTerritoryId。 DimSalesTerritory 表中的 SalesTerritoryId 列包含对应于每个销售区域的不同 ID 值。  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>在 FactInternetSales、DimGeography 与 DimSalesTerritory 表之间创建关系  
  
1.  在“关系图视图”中的“DimGeography”表内，单击并按住“SalesTerritoryId”列，将光标拖到“DimSalesTerritory”表中的“SalesTerritoryId”列，然后松开鼠标。  
  
2.  在“FactInternetSales”表中，单击并按住“SalesTerritoryId”列，将光标拖到“DimSalesTerritory”表中的“SalesTerritoryId”列，然后松开鼠标。  
  
    请注意，此关系的“活动”属性为 False，表示它处于非活动状态。 FactInternetSales 表已有另一个活动关系。  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>在客户端应用程序中隐藏 EmployeeSecurity 表  
此任务隐藏 EmployeeSecurity 表，防止它显示在客户端应用程序的字段列表中。 请注意，隐藏某个表并不会对它提供保护。 用户仍可以查询 EmployeeSecurity 表数据，（如果知道怎样查询）。 若要保护 EmployeeSecurity 表数据以防止用户查询其中的任何数据，可在后面的任务中应用筛选器。  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>在客户端应用程序中隐藏 EmployeeSecurity 表  
  
-   在模型设计器的“关系图视图”中，右键单击“员工”表标题，并单击“从客户端工具中隐藏”。  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>创建“区域销售员工”用户角色  
此任务将创建用户角色。 此角色包含一个行筛选器，用于定义要向用户显示哪些 DimSalesTerritory 表行。 然后，该筛选器将以一个对多的关系方向应用到与 DimSalesTerritory 相关的其他所有表。 还可以应用一个筛选器来保护整个 EmployeeSecurity 表，防止属于该角色的任何用户查询该表。  
  
> [!NOTE]  
> 在本课程中创建的“区域销售员工”角色将成员限制为只能浏览（或查询）他们所属销售区域的销售数据。 如果将某个用户作为成员添加到“区域销售员工”角色，并且该用户同时以成员的身份充当[第 11 课：创建角色](../tutorials/aas-lesson-11-create-roles.md)中创建的角色，则会获得组合权限。 如果用户是多个角色的成员，则针对每个角色定义的权限和行筛选器可以累计。 也就是说，该用户会获得组合角色确定的更高权限。  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>创建“区域销售员工”用户角色  
  
1.  在 SSDT 中单击“模型”菜单，并单击“角色”。  
  
2.  在“角色管理器”中单击“新建”。  
  
    随后会将一个“无”权限的新角色添加到列表中。  
  
3.  单击该新角色，然后在“名称”列中将它重命名为“区域销售员工”。  
  
4.  在“权限”列中，单击下拉列表，并选择“读取”权限。  
  
5.  单击“成员”选项卡，并单击“添加”。  
  
6.  在“选择用户或组”对话框中的“输入要选择的对象名称”内，键入创建 EmployeeSecurity 表时使用的第一个示例用户名。 单击“检查名称”验证该用户名是否有效，并单击“确定”。  
  
    重复此步骤添加创建 EmployeeSecurity 表时使用的其他示例用户名称。  
  
7.  单击“行筛选器”选项卡。  
  
8.  对于“EmployeeSecurity”表，请在“DAX 筛选器”列中键入以下公式：  
  
    ```
      =FALSE()  
    ```
  
    此公式指定将所有列解析为 false 布尔条件。 “区域销售员工”用户角色成员不能查询 EmployeeSecurity 表中的任何列。  
  
9. 对于“DimSalesTerritory”表，请键入以下公式：  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    此公式中，LOOKUPVALUE 函数返回 DimEmployeeSecurity[SalesTerritoryId] 列的所有值，其中，EmployeeSecurity[LoginId] 是与当前的 Windows 登录用户名相同，EmployeeSecurity[SalesTerritoryId] 与 DimSalesTerritory[SalesTerritoryId] 相同。  
  
    然后，使用 LOOKUPVALUE 返回的销售区域 ID 集限制 DimSalesTerritory 表中显示的行。 只会显示其行 SalesTerritoryID 在 LOOKUPVALUE 函数返回的 ID 集内的行。  
  
10. 在角色管理器中单击“确定”。  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>测试“区域销售员工”用户角色  
此任务使用 SSDT 中的“在 Excel 中分析”功能测试“区域销售员工”用户角色的有效性。 指定已作为角色成员添加到 EmployeeSecurity 表的用户名之一。 然后，此用户名将用作在 Excel 与模型之间创建的连接中的有效用户名。  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>测试“区域销售员工”用户角色  
  
1.  在 SSDT 中单击“模型”菜单，并单击“在 Excel 中分析”。  
  
2.  在“在 Excel 中分析”对话框中的“指定用于连接到模型的用户名或角色”内，选择“其他 Windows 用户”，然后单击“浏览”。  
  
3.  在“选择用户或组”对话框中的“输入要选择的对象名称”内，键入已包含在 EmployeeSecurity 表中的用户名，然后单击“检查名称”。  
  
4.  单击“确定”关闭“选择用户或组”对话框，并单击“确定”关闭“在 Excel 中分析”对话框。  
  
    将在 Excel 中打开新的工作簿。 会自动创建一个数据透视表。 “数据透视表字段”列表包含新模型中可用的大多数数据字段。  
  
    请注意，EmployeeSecurity 表在数据透视表字段列表中不可见。 已在之前的任务中对客户端工具隐藏此表。  
  
5.  在“字段”列表中的“∑ Internet 销售”（度量值）内，选择“InternetTotalSales”度量值。 该度量值将输入到“值”字段中。  
  
6.  从“DimSalesTerritory”表中选择“SalesTerritoryId”列。 该列将输入到“行标签”字段中。  
  
    请注意，只显示所用有效用户名所属的一个区域的 Internet 销售数字。 如果从 DimGeography 表中选择另一列（例如“城市”）作为“行标签”字段，则只会显示有效用户所属销售区域中的城市。  
  
    此用户无法浏览或查询他们所不属于的区域的任何 Internet 销售数据。 之所以实施此限制，是因为在“区域销售员工”用户角色中针对 DimSalesTerritory 表定义的行筛选器保护与其他销售区域相关的所有数据。  
  
## <a name="see-also"></a>另请参阅  
[USERNAME 函数 (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE 函数 (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA 函数 (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  