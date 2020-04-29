---
title: 创建、开发和维护 Azure Synapse Studio （预览版）笔记本
description: 本文介绍如何创建和开发 Azure Synapse Studio （预览版）笔记本，以便进行数据准备和可视化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430222"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>创建、开发和维护 Azure Synapse Studio （预览版）笔记本

Azure Synapse Studio （预览版）笔记本是一个 web 界面，用于创建包含实时代码、可视化效果和叙述性文本的文件。 笔记本是验证想法并使用快速试验从数据中获取见解的好地方。 笔记本还广泛用于数据准备、数据可视化、机器学习和其他大数据方案。

使用 Azure Synapse Studio 笔记本，可以：

* 开始进行零设置工作。
* 利用内置企业安全功能使数据保持安全。
* 跨原始格式（CSV、txt、JSON 等）、处理的文件格式（parquet、增量 Lake、ORC 等）以及 SQL 表格数据文件和 Spark 和 SQL 进行分析。
* 利用增强的创作功能和内置的数据可视化功能提高工作效率。

本文介绍如何在 Azure Synapse Studio 中使用笔记本。

## <a name="create-a-notebook"></a>创建笔记本

可以通过两种方法创建笔记本。 你可以从**对象资源管理器**创建新笔记本或将现有笔记本导入到 Azure Synapse 工作区。 Azure Synapse Studio 笔记本可识别标准 Jupyter Notebook IPYNB 文件。

![synapse-import-笔记本](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>开发笔记本

笔记本包含单元，单元是单独的代码块或可单独运行或作为组运行的文本。

### <a name="add-a-cell"></a>添加单元格

有多种方法可向笔记本中添加新单元。

1. 展开左上方的 " **+ 单元**" 按钮，然后选择 "**添加代码单元**" 或 "**添加文本单元**"。

    ![添加单元-单元按钮](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 将鼠标悬停在两个单元格的空白上方，然后选择 "**添加代码**" 或 "**添加文本**"

    ![添加-单元格间距](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按**下将单元**格插入当前单元格之上。 按**B**在当前单元格的下方插入一个单元格。

### <a name="set-a-primary-language"></a>设置主要语言

Azure Synapse Studio 笔记本支持四种 spark 语言：

* pyspark （python）
* spark （Scala）
* sparkSQL
* Spark.NET （c #）

可以从顶部命令栏中的下拉列表设置新添加的单元格的主要语言。

   ![synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多种语言

可以通过在单元开头指定正确的语言魔术命令，在一个笔记本中使用多种语言。 下表列出了用于切换单元语言的幻命令。

|幻命令 |语言 | 说明 |  
|---|------|-----|
|%% pyspark| Python | 针对 Spark 上下文执行**Python**查询。  |
|%% spark| Scala | 针对 Spark 上下文执行**Scala**查询。  |  
|%% sql| SparkSQL | 针对 Spark 上下文执行**SparkSQL**查询。  |
|%% csharp | Spark.NET C# | 对 Spark 上下文执行**Spark.NET c #** 查询。 |

下图是一个示例，说明如何使用 **%% PySpark**幻命令或 SparkSQL 查询编写 PySpark 查询，该查询使用**Spark （Scala）** 笔记本中的 **%% sql**幻命令。 请注意，笔记本的主要语言设置为 Scala。

   ![synapse-magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用临时表跨语言引用数据

不能直接在 Synapse Studio 笔记本中跨不同语言引用数据或变量。 在 Spark 中，可以跨语言引用临时表。 下面是一个示例，说明如何在中`Scala` `PySpark`读取数据帧并`SparkSQL`使用 Spark 临时表作为解决方法。

1. 在单元格1中，使用 Scala 从 SQL 池连接器读取数据帧，并创建一个临时表。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 在第2单元中，使用 Spark SQL 查询数据。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在单元格3中，使用 PySpark 中的数据。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 样式 IntelliSense

Azure Synapse Studio 笔记本与摩纳哥编辑器集成，以将 IDE 样式 IntelliSense 引入到单元格编辑器。 语法突出显示、错误生成器和自动代码完成有助于您编写代码并更快地确定问题。

对于不同的语言，IntelliSense 功能处于不同的成熟度级别。 使用下表查看支持的内容。

|Languages| 语法突出显示 | 语法错误标记  | 语法代码完成 | 变量代码完成| 系统函数代码完成| 用户函数代码完成| 智能缩进 | 代码折叠|
|--|--|--|--|--|--|--|--|--|
|PySpark （Python）|是|是|是|是|是|是|是|是|
|Spark （Scala）|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|Spark.NET （c #）|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>用工具栏按钮设置文本单元格的格式

您可以使用 "文本单元" 工具栏中的 "格式" 按钮执行常见的 markdown 操作。 它包括粗体文本、italicizing 文本、插入代码段、插入未排序列表、插入有序列表以及从 URL 插入图像。

  ![synapse-文本单元格-工具栏](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>撤消单元操作
单击 "**撤消**" 按钮或按**Ctrl + Z**撤消最近的单元格操作。 现在，您可以撤销最新的20个历史单元操作。 

   ![synapse](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移动单元

选择省略号（...）以访问最右侧的其他单元格操作菜单。 然后选择 "**上移单元**" 或 "下移**单元**" 来移动当前单元格。 

你还可以使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按**Ctrl + Alt + ↑**上移当前单元格。 按**Ctrl + Alt + ↓**将当前单元向下移动。

   ![移动单元](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>删除单元格

若要删除单元，请选择省略号（...）以访问最右侧的 "其他单元格操作" 菜单，然后选择 "**删除单元格**"。 

你还可以使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按**d，d**删除当前单元格。
  
   ![删除-单元格](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>折叠单元格输入
单击当前单元格底部的箭头按钮以将其折叠。 若要展开它，请在折叠单元格时单击箭头按钮。

   ![折叠单元格-输入](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>折叠单元格输出

单击当前单元格输出的左上角的 "**折叠输出**" 按钮，将其折叠。 若要展开该方法，请在折叠单元格输出时单击 "**显示单元格输出**"。

   ![折叠单元-输出](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>运行笔记本

你可以逐个或同时在笔记本中运行代码单元。 每个单元格的状态和进度都在笔记本中表示。

### <a name="run-a-cell"></a>运行单元

可以通过多种方法在单元格中运行代码。

1. 将鼠标悬停在要运行的单元格上，并选择 "**运行单元**" 按钮或按**Ctrl + enter**。

   ![单元格-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 若要访问最右侧的 "其他单元格操作" 菜单，请选择省略号（**...**）。然后选择 "**运行单元格**"。

   ![单元格-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 使用[命令模式下的快捷键](#shortcut-keys-under-command-mode)。 按**Shift + Enter**运行当前单元格并选择下面的单元格。 按**Alt + Enter**运行当前单元格，并在下方插入一个新单元。


### <a name="run-all-cells"></a>运行所有单元格
单击 "**全部运行**" 按钮以按顺序运行当前笔记本中的所有单元格。

   ![全部运行单元](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>运行上面或下面的所有单元

若要访问最右侧的 "其他单元格操作" 菜单，请选择省略号（**...**）。然后，选择 "**运行上方单元格**" 以按顺序运行高于当前的所有单元格。 选择 "**运行下面的单元格**" 以按顺序运行当前下的所有单元格。

   ![运行-单元格之上-或-下方](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>单元格状态指示器

逐步骤单元格执行状态显示在单元格下方，以帮助你查看其当前进度。 单元格运行完成后，将显示具有总持续时间和结束时间的执行摘要，并将其保留在此处供将来参考。

![单元格-状态](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 进度指示器

Azure Synapse Studio 笔记本仅基于 Spark。 代码单元远程在 Spark 池上执行。 此时会提供 Spark 作业进度指示器，其中显示了实时进度栏，可帮助你了解作业执行状态。


![spark-进度指示器](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 会话配置

可以指定要在**配置会话**中为当前 Spark 会话指定的超时持续时间、数字和执行器的大小。 重新启动 Spark 会话是为了使配置更改生效。 所有缓存的笔记本变量都将被清除。

![会话管理](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>将数据引入笔记本

如以下代码示例中所示，可以从 Azure Blob 存储、Azure Data Lake Store 第2代和 SQL 池中加载数据。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>从 Azure Data Lake Store Gen2 作为 Spark 数据帧读取 CSV

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>作为 Spark 数据帧从 Azure Blob 存储读取 CSV

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

你可以直接访问主存储帐户中的数据。 无需提供机密密钥。 在数据资源管理器中，右键单击某个文件，然后选择 "**新建笔记本**" 以查看包含自动生成的数据提取器的新笔记本。

![数据到单元格](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>可视化笔记本中的数据

### <a name="display"></a>显示（）

表格结果视图附带了创建条形图、折线图、饼图、散点图和面积图的选项。 无需编写代码即可直观显示数据。 可以在**图表选项**中自定义这些图表。 

默认情况下，在呈现的表视图中显示 **%% sql**幻命令的输出。 您可以对 Spark DataFrames 或弹性分布式数据集（RDD）函数调用**display （`<DataFrame name>`）** 以生成呈现的表视图。

   ![内置-图表](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

可以使用**displayHTML （）** 来呈现 HTML 或交互式库，如**bokeh**。

下图是使用**bokeh**在地图上绘制字型的示例。

   ![bokeh-示例](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

1. 若要保存对单个笔记本所做的更改，请选择笔记本命令栏上的 "**发布**" 按钮。

   ![发布-笔记本](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 若要保存工作区中的所有笔记本，请在工作区命令栏上选择 "**全部发布**" 按钮。 

   ![发布-全部](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在笔记本属性中，可以配置保存时是否包括单元格输出。

   ![笔记本-属性](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>幻命令
可以在 Azure Synapse Studio 笔记本中使用熟悉的 Jupyter 幻命令。 检查以下列表中是否有当前可用的幻命令。 告诉我们 GitHub 上的用例，以便我们可以继续构建更复杂的命令来满足你的需求。

可用行 magic： [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)， [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用单元 magic： [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)， [%% capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)， [%% writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)， [%% sql](#use-multiple-languages)， [%% pyspark](#use-multiple-languages)， [%% spark](#use-multiple-languages)， [%% csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>快捷键

类似于 Jupyter 笔记本，Azure Synapse Studio 笔记本具有模式用户界面。 键盘根据笔记本单元的具体模式执行不同的任务。 Synapse Studio 笔记本支持给定代码单元的以下两种模式：命令模式和编辑模式。

1. 当没有文本光标提示您键入时，单元格处于命令模式。 当单元格处于命令模式时，可以将笔记本作为一个整体进行编辑，但不能将其键入单独的单元格中。 通过按`ESC`或使用鼠标在单元格的编辑器区域外单击来进入命令模式。

   ![命令模式](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 编辑模式由文本光标指示，提示您在编辑器区域中键入内容。 当单元格处于编辑模式时，您不能在该单元格中键入。 按下`Enter`或使用鼠标单击某个单元格的编辑器区域，进入编辑模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快捷键

使用以下击键快捷方式，你可以更轻松地在 Azure Synapse 笔记本中导航并运行代码。

| 操作 |Synapse Studio 笔记本快捷方式  |
|--|--|
|运行当前单元格并选择下面的 | Shift+Enter |
|运行当前单元格并在下方插入 | Alt+Enter |
|选择上方单元格| 向上 |
|选择下方单元| 向下 |
|在上方插入单元格| A |
|在下方插入单元格| B |
|扩展所选单元格| Shift + 向上键 |
|扩展所选单元格| Shift + 向下键|
|向上移动单元| Ctrl + Alt + ↑ |
|向下移动单元| Ctrl + Alt + ↓ |
|删除所选单元| D、D |
|切换到编辑模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>编辑模式下的快捷键

使用以下击键快捷方式，可以在编辑模式下更轻松地在 Azure Synapse 笔记本中导航并运行代码。

| 操作 |Synapse Studio 笔记本快捷方式  |
|--|--|
|向上移动光标 | 向上 |
|向下移动光标|向下|
|撤消|Ctrl + Z|
|重做|Ctrl + Y|
|注释/取消注释|Ctrl +/|
|删除字词|Ctrl + Backspace|
|删除字词|Ctrl + Delete|
|中转到单元格起始|Ctrl + Home|
|跳到单元格末尾 |Ctrl + End|
|左移一个字|Ctrl + 向左键|
|右移一个字|Ctrl + 向右键|
|全选|Ctrl + A|
|降级| Ctrl +]|
|Dedent|Ctrl + [|
|切换到命令模式| Esc |

## <a name="next-steps"></a>后续步骤

- [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
