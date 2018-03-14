---
title: "从 Excel、Python 或 R 连接到 Azure Databricks | Microsoft Docs"
description: "了解如何使用 Simba 驱动程序来将 Azure Databricks 连接到 Excel、Python 或 R。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 9daa7d30036d0a0f98d079e03a69c29d11e49664
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>从 Excel、Python 或 R 连接到 Azure Databricks

在本文中，你将学习如何使用 Microsoft Excel、Python 或 R 语言通过 Databricks ODBC 驱动程序来连接 Azure Databricks。 在建立连接后，可以从 Excel、Python 或 R 客户端访问 Azure Databricks 中的数据。 还可以使用这些客户端来进一步分析数据。 

## <a name="prerequisites"></a>先决条件

* 必须具有 Azure Databricks 工作区、Spark 群集以及与群集关联的示例数据。 如果尚不具备这些先决条件，请完成[使用 Azure 门户在 Azure Databricks 上运行 Spark 作业](quickstart-create-databricks-workspace-portal.md)中的快速入门。

* 从 [Databricks 驱动程序下载页面](https://databricks.com/spark/odbc-driver-download)下载 Databricks ODBC 驱动程序。 根据要从中连接到 Azure Databricks 的应用程序安装 32 位或 64 位版本。 例如，若要从 Excel 进行连接，请安装该驱动程序的 32 位版本。 若要从 R 和 Python 进行连接，请安装该驱动程序的 64 位版本。

* 在 Databricks 中设置个人访问令牌。 有关说明，请参阅[令牌管理](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)。

## <a name="set-up-a-dsn"></a>设置 DSN

数据源名称 (DSN) 包含有关特定数据源的信息。 ODBC 驱动程序需要使用此 DSN 来连接到数据源。 在本部分中，你将设置可以用于 Databricks ODBC 驱动程序的 DSN，以便从 Microsoft Excel、Python 或 R 等客户端连接到 Azure Databricks。

1. 从 Azure Databricks 工作区中，导航到 Databricks 群集。

    ![打开 Databricks 群集](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "打开 Databricks 群集")

2. 在“配置”选项卡下，单击“JDBC/ODBC”选项卡并复制“服务器主机名”和“HTTP 路径”的值。 需要使用这些值来完成本文中的步骤。

    ![获取 Databricks 配置](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "获取 Databricks 配置")

3. 在计算机上，根据所用的应用程序启动 **ODBC 数据源**应用程序（32 位或 64 位）。 若要从 Excel 进行连接，请使用 32 位版本。 若要从 R 和 Python 进行连接，请使用 64 位版本。

    ![启动 ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "启动 ODBC 应用")

4. 在“用户 DSN”选项卡下，单击“添加”。 在“创建新数据源”对话框中，选择“Simba Spark ODBC 驱动程序”，然后单击“完成”。

    ![启动 ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "启动 ODBC 应用")

5. 在“Simba Spark ODBC 驱动程序”对话框中，提供以下值：

    ![配置 DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "配置 DSN")

    下表提供了有关要在对话框中提供的值的信息。
    
    |字段  | 值  |
    |---------|---------|
    |**数据源名称**     | 为数据源提供一个名称。        |
    |**主机**     | 提供从 Databricks 工作区中为*服务器主机名*复制的值。        |
    |**端口**     | 输入 *443*。        |
    |**身份验证** > **机制**     | 选择“用户名和密码”。        |
    |**用户名**     | 输入 *token*。        |
    |**密码**     | 输入从 Databricks 工作区中复制的令牌值。 |
    
    在“DSN 设置”对话框中执行以下附加步骤。
    
    * 单击“HTTP 选项”。 在打开的对话框中，粘贴从 Databricks 工作区中复制的“HTTP 路径”值。 单击“确定”。
    * 单击“SSL 选项”。 在打开的对话框中，选中“启用 SSL”复选框。 单击“确定”。
    * 单击“测试”以测试到 Azure Databricks 的连接。 单击“确定”以保存配置。
    * 在“ODBC 数据源管理器”对话框中，单击“确定”。

现在已设置了 DSN。 在接下来的各部分中，你将使用此 DSN 从 Excel、Python 或 R 连接到 Azure Databricks。

## <a name="connect-from-microsoft-excel"></a>从 Microsoft Excel 进行连接

在本部分中，你将使用之前创建的 DSN 将数据从 Azure Databricks 拉取到 Microsoft Excel 中。 在开始之前，请确保已在计算机上安装了 Microsoft Excel。 可以使用从 [Microsoft Excel 试用版链接](https://products.office.com/excel)获取的 Excel 试用版。

1. 在 Microsoft Excel 中打开一个空白工作簿。 在“数据”功能区中，单击“获取数据”。 单击“从其他源”，然后单击“从 ODBC”。

    ![从 Excel 启动 ODBC](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "从 Excel 启动 ODBC")

2. 在“从 ODBC”对话框中，选择之前创建的 DSN，然后单击“确定”。

    ![选择 DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "选择 DSN")

3. 如果系统提示输入凭据，对于用户名，输入 **token**。 对于密码，提供从 Databricks 工作区中检索到的令牌值。

    ![为 Databricks 提供凭据](./media/connect-databricks-excel-python-r/excel-databricks-token.png "选择 DSN")

4. 从导航器窗口中，选择 Databricks 中要加载到 Excel 的表，然后单击“加载”。 

    ![将数据加载到 Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "将数据加载到 Excel")

将数据加载到 Excel 工作簿后，可以对其执行分析操作。

## <a name="connect-from-r"></a>从 R 进行连接

在本部分中，你将使用 R 语言 IDE 来引用 Azure Databricks 中可用的数据。 在开始之前，必须在计算机上安装以下各项。

* R 语言的 IDE。 本文中使用了 RStudio for Desktop。 可以从 [R Studio 下载](https://www.rstudio.com/products/rstudio/download/)安装该软件。
* 如果使用 RStudio for Desktop 作为 IDE，还需要从 [http://aka.ms/rclient/](http://aka.ms/rclient/) 安装 Microsoft R 客户端。 

打开 RStudio 并执行以下步骤：

- 引用 `RODBC` 包。 这使得你可以使用之前创建的 DSN 连接到 Azure Databricks。
- 使用 DSN 建立连接。
- 对 Azure Databricks 中的数据运行 SQL 查询。 在以下代码片段中，*radio_sample_data* 是 Azure Databricks 中已存在的表。
- 对查询执行一些操作来验证输出。 

下面的代码片段执行以下任务：

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>从 Python 进行连接

在本部分中，你将使用 Python IDE（例如 IDLE）来引用 Azure Databricks 中可用的数据。 在开始之前，请完成以下先决条件：

* 从[此处](https://www.python.org/downloads/)安装 Python。 从该链接安装 Python，还要安装 IDLE。

* 在计算机上的命令提示符下，安装 `pyodbc` 包。 运行以下命令：

      pip install pyodbc

打开 IDLE 并执行以下步骤：

- 导入 `pyodbc` 包。 这使得你可以使用之前创建的 DSN 连接到 Azure Databricks。
- 使用之前创建的 DSN 建立连接。
-  使用所创建的连接运行 SQL 查询。 在以下代码片段中，*radio_sample_data* 是 Azure Databricks 中已存在的表。
- 对查询执行操作来验证输出。

下面的代码片段执行以下任务：

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>后续步骤

* 若要了解可以从中将数据导入到 Azure Databricks 中的源，请参阅 [Azure Databricks 的数据源](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


