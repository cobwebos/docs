---
title: 在 Azure 数据目录中注册数据资产
description: 如何在 Azure 数据目录中注册数据资产
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 6dcf29c1bb98d15daf652671f31ed1647d66a81b
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68735200"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>教程：在 Azure 数据目录中注册数据资产

本教程介绍如何使用注册工具将 Azure SQL 数据库示例中的数据资产注册到目录。 注册是从数据源及其包含的资产中提取关键结构元数据（例如名称、类型和位置），并将该元数据复制到目录的过程。 数据源与数据资产保留在原地，但目录使用元数据，使数据源及其数据更容易发现和识别。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 注册数据资产 
> * 搜索数据资产
> * 批注数据资产
> * 连接到数据资产
> * 管理数据资产
> * 删除数据资产

## <a name="prerequisites"></a>先决条件

在开始之前，必须先完成[快速入门](register-data-assets-tutorial.md)。

* [Microsoft Azure](https://azure.microsoft.com/) 订阅。
* 需要有自己的 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

只有 Azure 订阅的所有者或共同所有者才可以设置数据目录。

## <a name="register-data-assets"></a>注册数据资产

### <a name="register-a-data-source"></a>注册数据源

你将注册 [Azure SQL 数据库示例](../sql-database/sql-database-single-database-get-started.md)中的数据资产（表），但如果想要使用熟悉且与角色相关的数据，也可以使用任何支持的数据源。 有关支持的数据源列表，请参阅 [Supported data sources](data-catalog-dsr.md)（支持的数据源）。

本教程使用的 Azure SQL 数据库名称为 *RLSTest*。

现在，可以使用 Azure 数据目录来注册 Azure SQL 数据库示例中的数据资产。

1. 转到 [Azure 数据目录主页](http://azuredatacatalog.com)，选择“发布数据”。 

   ![Azure 数据目录 -“发布数据”按钮](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. 选择“启动应用程序”，在计算机上下载、安装并运行注册工具。 

   ![Azure 数据目录 -“启动”按钮](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. 在“欢迎”页上，选择“登录”并输入凭据。  

    ![Azure 数据目录 -“欢迎”页](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. 在“Microsoft Azure 数据目录”页上，依次选择“SQL Server”、“下一步”。   

    ![Azure 数据目录 - 数据源](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. 输入 Azure SQL 数据库示例的 SQL Server 连接属性，然后选择“连接”。 

   ![Azure 数据目录 - SQL Server 连接设置](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. 注册数据资产的元数据。 本示例将注册 Azure SQL 数据库示例命名空间中的 **Product** 对象：

    1. 在“服务器层次结构”树中，展开 Azure SQL 数据库示例并选择“SalesLT”。  

    2. 在按住 Ctrl 键的同时，选择“Product”、“ProductCategory”、“ProductDescription”和“ProductModel”。    

    3. 选择**向右箭头** ( **>** )。 此操作将所有选定的对象移到“要注册的对象”列表中。 

          ![Azure 数据目录教程 - 浏览和选择对象](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. 选择“包括预览”以包含数据的快照预览。  快照中包含最多 20 条来自各个表的记录，并且会复制到目录。

    5. 选择“包括数据配置文件”以包含数据配置文件的对象统计信息快照（例如：列的最小值、最大值和平均值以及行数）。 

    6. 在“添加标记”字段中，输入 **sales, product, azure sql**。  此操作添加这些数据资产的搜索标记。 标记可帮助用户查找已注册的数据源，非常有用。

    7. 指定此数据的 **专家** 名称（可选）。

          ![Azure 数据目录教程 - 要注册的对象](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. 选择“注册”  。 Azure 数据目录将注册选定的对象。 本练习将注册 Azure SQL 数据库示例中的选定对象。 注册工具从数据资产提取元数据，将该数据复制到 Azure 数据目录服务。 数据将保留在其当前所处位置。 数据仍受原始系统的管理员和策略的控制。

          ![Azure 数据目录 - 已注册的对象](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. 若要查看注册的数据源对象，请选择“查看门户”。  在 Azure 数据目录门户中，确认可在网格视图中看到所有四个表和数据库（检查搜索栏中是否无搜索内容）。

        ![Azure 数据目录门户中的对象](media/register-data-assets-tutorial/data-catalog-view-portal.png)

本练习已注册 Azure SQL 数据库示例中的对象，使整个组织中的用户可以轻松找到它们。

下一个练习介绍如何发现已注册的数据资产。

## <a name="discover-data-assets"></a>发现数据资产

Azure 数据目录中的发现使用两个主要机制：搜索和筛选。

搜索机制直截了当且功能强大。 默认情况下，会根据目录中的任何属性（包括用户提供的批注）来匹配搜索词。

筛选是对搜索的补充。 可以选择特定特征，例如专家、数据源类型、对象类型和标记，来查看匹配的数据资产并将搜索结果限制为匹配的资产。

将搜索与筛选结合使用，可以快速浏览已注册到 Azure 数据目录的数据源。

本练习使用 Azure 数据目录门户发现上一练习中注册的数据资产。 有关搜索语法的详细信息，请参阅 [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) （数据目录搜索语法参考）。

下面是发现目录中数据资产的几个示例。  

### <a name="discover-data-assets-with-basic-search"></a>使用基本搜索发现数据资产

基本搜索可帮助使用一个或多个搜索词搜索目录。 结果是与一个或多个指定搜索词的属性匹配的所有资产。

1. 在 Azure 数据目录门户中选择“主页”。  如果已关闭 Web 浏览器，请转到 [Azure 数据目录主页](https://www.azuredatacatalog.com)。

2. 在搜索框中输入 `product` ，并按 **ENTER**。

    ![Azure 数据目录 - 基本文本搜索](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. 确认是否在结果中看到了所有四个表和数据库。 可以选择工具栏上的按钮在**网格视图**与**列表视图**之间切换，如下图所示。 请注意，由于“突出显示”选项为“打开”，因此搜索结果中突出显示了搜索关键字。   **每页显示的结果数** 。

    ![Azure 数据目录 - 基本文本搜索结果](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    “搜索”面板位于左侧，“属性”面板位于右侧。   在“搜索”面板中，可以更改搜索条件和筛选结果。  “属性”面板显示网格或列表中所选对象的属性。 

4. 在搜索结果中选择“Product”。  选择“预览”、“列”、“数据配置文件”和“文档”选项卡，或选择箭头展开底部窗格。      

    ![Azure 数据目录 - 底部窗格](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    在“预览”选项卡上，可以看到 **Product** 表中数据的预览。   
5. 选择“列”选项卡查找有关数据资产列的详细信息（例如**名称**和**数据类型**）。 

6. 选择“数据配置文件”选项卡查看数据资产中数据的分析（例如：行数、数据大小或列中的最小值）。 

### <a name="discover-data-assets-with-property-scoping"></a>使用属性范围发现数据资产

属性范围可帮助发现搜索词符合指定属性的数据资产。

1. 清除“筛选器”中“对象类型”下面的“表”筛选器。     

2. 在搜索框中输入 `tags:product` ，并按 **ENTER**。 有关可用于搜索数据目录的所有属性，请参阅 [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) （数据目录搜索语法参考）。

3. 确认是否在结果中看到了这些表和数据库。  

    ![数据目录 - 属性范围搜索结果](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>保存搜索

1. 在“当前搜索”部分的“搜索”窗格中，输入搜索的名称并选择“保存”。   

    ![Azure 数据目录 - 保存搜索](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. 确认已保存的搜索显示在“已保存的搜索”下面。 

3. 选择可对已保存的搜索执行的操作（“重命名”、“删除”、“设为默认值”搜索）。   

### <a name="grouping-with-parentheses"></a>使用括号分组

使用括号分组可将一部分查询分组以实现逻辑隔离，尤其是与布尔运算符结合使用时。

1. 在搜索框中输入 `name:product AND (tags:product AND objectType:table)` ，并按 **ENTER**。

2. 确认搜索结果中只显示了 **Product** 表。

    ![Azure 数据目录 - 分组搜索](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>比较运算符

使用比较运算符可以针对具有数字和日期数据类型的属性使用比较而非相等。

1. 在搜索框中输入 `lastRegisteredTime:>"06/09/2016"`。

2. 清除“对象类型”下面的“表”筛选器。  

3. 按 **ENTER**。

4. 确认搜索结果中显示了注册的 **Product**、**ProductCategory**、**ProductDescription** 表以及 Azure SQL 数据库。

    ![Azure 数据目录 - 比较搜索结果](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

有关发现数据资产的详细信息，请参阅[如何发现数据资产](data-catalog-how-to-discover.md)。 有关搜索语法的详细信息，请参阅[数据目录搜索语法参考](/rest/api/datacatalog/#search-syntax-reference)。

## <a name="annotate-data-assets"></a>批注数据资产

本练习使用 Azure 数据目录门户来批注目录中的现有数据资产（添加说明、标记或专家等信息）。 批注将会补充在注册期间从数据源提取的结构元数据。 批注能够大大简化数据资产的发现与理解。

本练习将批注一个数据资产 (ProductPhoto)。 为 ProductPhoto 数据资产添加友好名称和描述。  

1. 转到 [Azure 数据目录主页](https://www.azuredatacatalog.com)，使用 `tags:product` 进行搜索，以找到注册的数据资产。

2. 在搜索结果中选择“ProductModel”。   

3. 在“友好名称”中输入“产品图像”，在“描述”中输入“市场营销材料的产品照片”。    

    ![Azure 数据目录 - ProductPhoto 描述](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    “描述”可帮助其他人发现并识别为何及如何使用选定的数据资产。  还可以添加更多标记和查看列。 可以使用已添加到目录的描述性元数据来搜索和筛选数据源。

还可以在此页上执行以下步骤：

* 添加数据资产的专家。 在“专家”区域中选择“添加”。  

* 添加数据集级别的标记。 在“标记”区域中选择“添加”。   标记可以是用户标记或词汇标记。 标准版的数据目录包包含帮助目录管理员定义中心业务分类的业务词汇。 然后，目录用户即可使用术语表术语对数据资产进行批注。 有关详细信息，请参阅 [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md)

* 添加列级别的标记。 针对想要批注的列，选择“标记”下面的“添加”。  

* 添加列级别的描述。 输入列的 **描述** 。 还可以查看从数据源提取的描述元数据。

* 添加“请求访问”信息，向用户显示如何请求对数据资产的访问权限。 
  
* 选择“文档”选项卡并提供数据资产的文档。  通过 Azure 数据目录文档，可以使用数据目录作为内容存储库，创建完整的数据资产叙述。
  
也可以针对多个数据资产添加一个批注。 例如，可以选择已注册的所有数据资产，以及指定这些资产的专家。

![Azure 数据目录 - 批注多个数据资产](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure 数据目录支持众包批注方式。 任何数据目录用户都可以添加标记（用户或词汇表）、说明和其他元数据。 这样，用户便可以针对数据资产及其用法添加见解，并与其他用户分享该见解。

有关批注数据资产的详细信息，请参阅 [How to annotate data assets](data-catalog-how-to-annotate.md) （如何批注数据资产）。

## <a name="connect-to-data-assets"></a>连接到数据资产

本练习使用连接信息在集成式客户端工具 (Excel) 和非集成式工具 (SQL Server Management Studio) 中打开数据资产。

> [!NOTE]
> 请务必记住，Azure 数据目录不允许访问实际数据源，它只能方便发现和识别数据源。 连接到数据源时，选择的客户端应用程序使用 Windows 凭据，或在必要时提示提供凭据。 如果以前未获得数据源的访问权限，则必须先获得授权才能连接。

### <a name="connect-to-a-data-asset-from-excel"></a>从 Excel 连接到数据资产

1. 在搜索结果中选择“Product”。  选择工具栏上的“打开方式”，然后选择“Excel”。  

    ![Azure 数据目录 - 连接到数据资产](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. 在下载弹出窗口中选择“打开”。  这种体验根据浏览器而有所不同。

3. 在“Microsoft Excel 安全声明”窗口中，选择“启用”。  

    ![Azure 数据目录 - Excel 安全声明弹出窗口](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. 保留“导入数据”对话框中的默认值，然后选择“确定”。  

    ![Azure 数据目录 - Excel 导入数据](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. 在 Excel 中查看数据源。

    ![Azure 数据目录 - Excel 中的 product 表](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

在本练习中，已连接到使用 Azure 数据目录发现的数据资产。 在 Azure 数据目录门户中，可以使用已集成到“打开方式”菜单中的客户端应用程序来直接建立连接。  也可以使用资产元数据中包含的连接位置信息来与选择的任何应用程序建立连接。 例如，可以使用 SQL Server Management Studio 连接到 Azure SQL 数据库，以访问本教程中注册的数据资产中的数据。

1. 打开 **SQL Server Management Studio**。

2. 在“连接到服务器”对话框中，输入 Azure 数据目录门户的“属性”窗格中显示的服务器名称。  

3. 使用适当的身份验证和凭据访问数据资产。 如果没有访问权限，请使用“请求访问”字段中的信息来获取访问权限。 

    ![Azure 数据目录 - 请求访问](media/register-data-assets-tutorial/data-catalog-request-access.png)

选择“查看连接字符串”查看 ADO.NET、ODBC 和 OLEDB 连接字符串，并将这些字符串复制到剪贴板，以便在应用程序中使用。 

## <a name="manage-data-assets"></a>管理数据资产

在此步骤中了解如何设置数据资产的安全性。 数据目录不允许用户访问数据本身。 数据源的所有者将控制数据访问权限。

使用数据目录可以发现数据源，以及查看与目录中注册的源相关的元数据。 但在某些情况下，只有特定用户或特定组的成员才能看到数据源。 对于这种情况，可以使用数据目录获取已注册数据资产的所有权，以及控制拥有的资产的可见性。

> [!NOTE]
> 本练习所述的管理功能只在标准版 Azure 数据目录中提供，免费版本不提供。
> 在 Azure 数据目录中，可以获取数据资产的所有权、添加数据资产的共同所有者，以及设置数据资产的可见性。

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>获取数据资产的所有权和限制可见性

1. 转到 [Azure 数据目录主页](https://www.azuredatacatalog.com)。 在“搜索”文本框中输入 `tags:cycles`，并按 **ENTER**。 

2. 在结果列表中选择某个项，然后选择工具栏上的“取得所有权”。 

3. 在“属性”面板的“管理”部分中，选择“取得所有权”。   

    ![Azure 数据目录 - 取得所有权](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. 若要限制可见性，请选择“可见性”部分的“所有者和这些用户”，然后选择“添加”。    **ENTER**。

    ![Azure 数据目录 - 限制访问](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>删除数据资产

本练习使用 Azure 数据目录门户从已注册的数据资产中删除预览数据，并从目录中删除数据资产。

在 Azure 数据目录中，可以删除单个资产或多个资产。

1. 转到 [Azure 数据目录主页](https://www.azuredatacatalog.com)。

2. 在“搜索”文本框中输入 `tags:cycles`，然后按 **ENTER**。 

3. 在结果列表中选择某个项，然后选择工具栏上的“删除”，如下图所示： 

    ![Azure 数据目录 - 删除网格项](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    如果使用列表视图，此复选框将出现在项的左侧，如下图所示：

    ![Azure 数据目录 - 删除列表项](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    也可以选择多个数据资产并将其删除，如下图所示：

    ![Azure 数据目录 - 删除多个数据资产](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> 目录的默认行为是允许任何用户注册任何数据源，允许任何用户删除任何已注册的数据资产。 标准版 Azure 数据目录中包含的管理功能提供其他选项，用于取得资产所有权、限制谁可以发现资产，以及谁可以删除资产。

## <a name="summary"></a>摘要

本教程介绍了 Azure 数据目录的基本功能，包括注册、批注、发现和管理企业数据资产。 完成本教程后，便可以开始使用此产品了。 一开始，可以注册自己和团队依赖的数据源，并邀请同事使用目录。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [支持的数据源](data-catalog-dsr.md)