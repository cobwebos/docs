---
title: Synapse Studio 笔记本
description: 本文介绍如何创建和开发 Azure Synapse Studio （预览版）笔记本，以便进行数据准备和可视化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: tracking-python
ms.openlocfilehash: e0b0525035732a54965f7c391ac6041b114d7304
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045682"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中创建、开发和维护 Synapse Studio （预览版）笔记本

Synapse Studio （预览版）笔记本是一个 web 界面，用于创建包含实时代码、可视化效果和叙述性文本的文件。 笔记本是验证想法并使用快速试验从数据中获取见解的好地方。 笔记本还广泛用于数据准备、数据可视化、机器学习和其他大数据方案。

借助 Azure Synapse Studio 笔记本，可以：

* 无需设置即可开始工作。
* 利用内置企业安全功能确保数据安全。
* 针对 Spark 和 SQL，分析跨原始格式（CSV、txt、JSON 等）、已处理的文件格式（parquet、Delta Lake、ORC 等）以及 SQL 表格数据文件的数据。
* 利用增强的创作功能和内置的数据可视化功能提高工作效率。

本文介绍如何在 Azure Synapse Studio 中使用笔记本。

## <a name="create-a-notebook"></a>创建笔记本

可通过两种方法创建笔记本。 可以从“对象资源管理器”创建新笔记本或将现有笔记本导入到 Azure Synapse 工作区。 Azure Synapse Studio 笔记本可识别标准 Jupyter Notebook IPYNB 文件。

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>开发笔记本

笔记本由单元格组成，单元格是单独的代码块或可单独运行或作为组运行的文本块。

### <a name="add-a-cell"></a>添加单元格

有多种方法可向笔记本添加新单元格。

1. 展开左上方“+ 单元格”按钮，然后选择“添加代码单元格”或“添加文本单元格”  。

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 将鼠标悬停在两个单元格之间的空白地方，然后选择“添加代码”或“添加文本” 。

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 A 在当前单元格上方插入单元格。 按 B 在当前单元格下方插入单元格。

### <a name="set-a-primary-language"></a>设置主要语言

Azure Synapse Studio 笔记本支持四种 Apache Spark 语言：

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET for Apache Spark (C#)

可以从顶部命令栏中的下拉列表为新添加的单元格设置主要语言。

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多种语言

通过在单元格开始位置指定正确的语言 magic 命令，可以在一个笔记本中使用多种语言。 下表列出了用于切换单元格语言的 magic 命令。

|magic 命令 |语言 | 说明 |  
|---|------|-----|
|%%pyspark| Python | 针对 Spark 上下文执行 Python 查询。  |
|%%spark| Scala | 针对 Spark 上下文执行 Scala 查询。  |  
|%%sql| SparkSQL | 针对 Spark 上下文执行 SparkSQL 查询。  |
|%%csharp | .NET for Spark C# | 针对 Spark 上下文执行 .NET for Spark C# 查询。 |

下图是一个示例，说明如何在 Spark(Scala) 笔记本中使用 %%pyspark magic 命令编写 PySpark 查询，或使用 %%sql magic 命令编写 SparkSQL 查询  。 请注意，笔记本的主要语言设置为 pySpark。

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用临时表跨语言引用数据

不能直接在 Synapse Studio 笔记本中跨不同语言引用数据或变量。 在 Spark 中，可以跨语言引用临时表。 下面是一个示例，说明如何使用 Spark 临时表作为解决方法，在 `PySpark` 和 `SparkSQL` 中读取 `Scala` 数据帧。

1. 在单元格 1 中，使用 Scala 从 SQL 池连接器读取数据帧，并创建一个临时表。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 在单元格 2 中，使用 Spark SQL 查询数据。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在单元格 3 中，使用 PySpark 中的数据。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 样式 IntelliSense

Azure Synapse Studio 笔记本集成了 Monaco 编辑器，将 IDE 样式的 IntelliSense 引入到了单元格编辑器中。 语法突出显示、错误标记和自动代码完成有助于您编写代码并更快地确定问题。

对于不同的语言，IntelliSense 功能处于不同的成熟度级别。 请参阅下表了解支持的功能。

|Languages| 语法突出显示 | 语法错误标记  | 语法代码补全 | 变量代码补全| 系统函数代码补全| 用户函数代码补全| 智能缩进 | 代码折叠|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|是|是|是|是|是|是|是|是|
|Spark (Scala)|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|.NET for Spark (C#)|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>通过工具栏按钮设置文本单元格的格式

可以使用文本单元格工具栏中的格式按钮执行常见的 markdown 操作。 它包括将文本设为粗体、将文本设为斜体、插入代码片段、插入未排序列表、插入已排序列表以及插入 URL 中的图像。

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>撤消单元格操作
单击“撤消”按钮或按 Ctrl+Z 可撤消最近的单元格操作 。 现在可以撤销最近的 20 个历史单元格操作。 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移动单元格

选择省略号 (…) 以访问最右侧的其他单元格操作菜单。 然后选择“上移单元格”或“下移单元格”移动当前单元格 。 

还可使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 Ctrl+Alt+↑，上移当前单元格。 按 Ctrl+Alt+↓，下移当前单元格。

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>删除单元格

要删除单元格，请选择省略号 (…) 以访问最右侧的其他单元格操作菜单，然后选择“删除单元格”。 

还可使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 D,D，删除当前单元格。
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>折叠单元格输入
单击当前单元格底部的箭头按钮，将其折叠。 要展开，请在单元格处于折叠状态时单击箭头按钮。

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>折叠单元格输出

单击当前单元格输出左上角的“折叠输出”按钮，将其折叠。 要展开，请在单元格输出处于折叠状态时单击“显示单元格输出”。

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>运行笔记本

你可以逐个或同时在笔记本中运行代码单元格。 每个单元格的状态和进度都在笔记本中表示。

### <a name="run-a-cell"></a>运行单元格

可以通过多种方法在单元格中运行代码。

1. 将鼠标悬停在要运行的单元格上，并选择“运行单元格”按钮，或按 Ctrl+Enter 。

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 要访问最右侧的其他单元格操作菜单，请选择省略号 (…)。然后选择“运行单元格”。

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按 Shift+Enter 运行当前单元格并选择下面的单元格。 按 Alt+Enter 运行当前单元格并在下面插入一个新单元格。


### <a name="run-all-cells"></a>运行所有单元格
单击“全部运行”按钮，按顺序运行当前笔记本中的所有单元格。

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>运行上方或下方的所有单元格

要访问最右侧的其他单元格操作菜单，请选择省略号 (…)。然后选择“运行上方的单元格”，按顺序运行当前单元格上方的所有单元格。 选择“运行下方的单元格”，按顺序运行当前单元格下方的所有单元格。

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>单元格状态指示器

逐步单元格执行状态显示在单元格下方，有助于你查看其当前进度。 单元格运行完成后，将显示具有总持续时间和结束时间的执行摘要，并将其保留在此处供将来参考。

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 进度指示器

Azure Synapse Studio 笔记本仅基于 Spark。 代码单元格在 Spark 池上远程执行。 Spark 作业进度指示器提供有实时进度栏，可帮助你了解作业执行状态。


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 会话配置

可以在“配置会话”中为当前 Spark 会话指定超时持续时间、数量和执行器的大小。 重启 Spark 会话，以使配置更改生效。 缓存的所有笔记本变量都将被清除。

![session-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>将数据引入笔记本

如以下代码示例所示，可以从 Azure Blob 存储、Azure Data Lake Store Gen 2 和 SQL 池加载数据。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>从 Azure Data Lake Store Gen2 读取 CSV 作为 Spark 数据帧

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>从 Azure Blob 存储读取 CSV 作为 Spark 数据帧

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>从主存储帐户读取数据

你可以直接访问主存储帐户中的数据。 无需提供密钥。 在数据资源管理器中，右键单击某个文件，然后选择“新建笔记本”以查看自动生成了数据提取器的新笔记本。

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>在笔记本中可视化数据

### <a name="display"></a>Display()

表格结果视图附带了创建条形图、折线图、饼图、散点图和分区图的选项。 无需编写代码即可可视化数据。 可以在“图表选项”中自定义这些图表。 

默认情况下，在呈现的表视图中显示 %%sql magic 命令的输出。 你可以对 Spark 数据帧或弹性分布式数据集 (RDD) 函数调用 display(`<DataFrame name>`)，以生成呈现的表视图。

   ![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

可以使用 displayHTML() 来呈现 HTML 或交互库，如 bokeh 。

下图是使用 bokeh 在地图上绘制字形的示例。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

运行下面的示例代码，绘制上面的图像。

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>保存笔记本

可以在工作区中保存单个笔记本或所有笔记本。

1. 要保存对单个笔记本所做的更改，请在笔记本命令栏上选择“发布”按钮。

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 要保存工作区中的所有笔记本，请在工作区命令栏上选择“发布所有”按钮。 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在笔记本属性中，可以配置保存时是否包括单元格输出。

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>magic 命令
可以在 Azure Synapse Studio 笔记本中使用熟悉的 Jupyter magic 命令。 查看以下列表了解当前可用的 magic 命令。 告诉我们你在 GitHub 上的用例，以便我们可以继续构建更多 magic 命令来满足你的需求。

可用行 magic：[%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用单元格 magic：[%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>快捷键

与 Jupyter Notebooks 类似，Azure Synapse Studio 笔记本具有模式用户界面。 键盘根据笔记本单元格的具体模式执行不同的任务。 Synapse Studio 笔记本支持给定代码单元格的以下两种模式：命令模式和编辑模式。

1. 当没有文本光标提示你键入时，单元格处于命令模式。 当单元格处于命令模式时，可以将笔记本作为一个整体进行编辑，但不能键入单独的单元格。 按 `ESC` 或使用鼠标单击单元格编辑器区域外部，进入命令模式。

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 编辑模式由文本光标指示，提示你在编辑器区域中键入内容。 当单元格处于编辑模式时，您可以在单元格中键入。 按 `Enter` 或使用鼠标单击单元格编辑器区域，进入编辑模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快捷键

使用以下击键快捷方式，你可以更轻松地在 Azure Synapse 笔记本中导航并运行代码。

| 操作 |Synapse Studio 笔记本快捷方式  |
|--|--|
|运行当前单元格并选择下方的单元格 | Shift+Enter |
|运行当前单元格并在下方插入 | Alt+Enter |
|选择上方的单元格| 向上 |
|选择下方的单元格| 向下 |
|在上方插入单元格| A |
|在下方插入单元格| B |
|展开上方的所选单元格| Shift+Up |
|展开下方的所选单元格| Shift+Down|
|上移单元格| Ctrl+Alt+↑ |
|下移单元格| Ctrl+Alt+↓ |
|删除所选单元格| D, D |
|切换到编辑模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>编辑模式下的快捷键

使用以下击键快捷方式，可以在编辑模式下更轻松地在 Azure Synapse 笔记本中导航并运行代码。

| 操作 |Synapse Studio 笔记本快捷方式  |
|--|--|
|上移光标 | 向上 |
|下移光标|向下|
|撤消|Ctrl+Z|
|重做|Ctrl+Y|
|注释/撤销注释|Ctrl+/|
|删除插入提示之前的字|Ctrl+Backspace|
|删除插入提示之后的字|Ctrl+Delete|
|转到单元格开头|Ctrl+Home|
|转到单元格末尾 |Ctrl+End|
|左移一个字|Ctrl+Left|
|右移一个字|Ctrl+Right|
|全选|Ctrl+A|
|缩进| Ctrl+]|
|取消缩进|Ctrl+[|
|切换到命令模式| Esc |

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Web 工具在 Azure Synapse Analytics 中创建 Apache Spark 池（预览版）](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics 中的 Apache Spark 是什么](apache-spark-overview.md)
- [将 .NET for Apache Spark 与 Azure Synapse Analytics 配合使用](spark-dotnet.md)
- [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
