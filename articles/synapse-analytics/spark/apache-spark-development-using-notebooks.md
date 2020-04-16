---
title: 创建、开发和维护 Azure 同步工作室（预览）笔记本
description: 在本文中，您将了解如何创建和开发 Azure Synapse Studio（预览）笔记本，以进行数据准备和可视化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430222"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>创建、开发和维护 Azure 同步工作室（预览）笔记本

Azure Synapse Studio（预览）笔记本是一个 Web 界面，用于创建包含实时代码、可视化效果和叙述文本的文件。 笔记本是验证想法和使用快速实验从数据中获取见解的好地方。 笔记本还广泛应用于数据准备、数据可视化、机器学习和其他大数据方案。

使用 Azure 同步工作室笔记本，您可以：

* 开始零设置工作。
* 通过内置的企业安全功能确保数据安全。
* 分析不同原始格式（CSV、txt、JSON 等）、已处理的文件格式（镶木地板、三角洲湖、ORC 等）的数据，以及针对 Spark 和 SQL 的 SQL 表格数据文件。
* 通过增强的创作功能和内置数据可视化提高工作效率。

本文介绍如何在 Azure 同步工作室中使用笔记本。

## <a name="create-a-notebook"></a>创建笔记本

创建笔记本有两种方法。 您可以创建新笔记本或将现有笔记本从**对象资源管理器**导入 Azure 同步工作区。 Azure Synapse 工作室笔记本可以识别标准 Jupyter 笔记本 IPYNB 文件。

![突触-创建-导入-笔记本](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>开发笔记本

笔记本由单元格组成，单元格是可独立运行或作为组运行的代码或文本的单个块。

### <a name="add-a-cell"></a>添加单元格

有多种方法可以向笔记本添加新单元格。

1. 展开左上角 **+ 单元格**按钮，然后选择 **"添加代码单元格**"或 **"添加文本单元格**"。

    ![带单元格的添加单元格按钮](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 将鼠标悬停在两个单元格之间的空间上，然后选择 **"添加代码**"或 **"添加文本**"。

    ![空间间的附加单元格](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 在[命令模式下使用快捷键](#shortcut-keys-under-command-mode)。 按**A**可在当前单元格上方插入单元格。 按**B**可在当前单元格下方插入单元格。

### <a name="set-a-primary-language"></a>设置主要语言

Azure Synapse 工作室笔记本支持四种火花语言：

* 火花（蛇）
* 火花（斯卡拉）
* 火花SQL
* Spark.NET （C#）

可以从顶部命令栏中的下拉列表中设置新添加的单元格的主语言。

   ![默认突触语言](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多种语言

通过在单元格的开头指定正确的语言魔术命令，可以在一个笔记本中使用多种语言。 下表列出了切换单元格语言的神奇命令。

|魔法命令 |语言 | 说明 |  
|---|------|-----|
|%%pyspark| Python | 针对 Spark 上下文执行**Python**查询。  |
|%%火花| Scala | 针对 Spark 上下文执行**Scala**查询。  |  
|%%sql| SparkSQL | 针对 Spark 上下文执行**SparkSQL**查询。  |
|%%csharp | Spark.NET C# | 针对 Spark 上下文执行**Spark.NET C#** 查询。 |

下图是如何使用 **%%-pyspark**魔术命令编写 PySpark 查询的示例，或者使用**Spark（Scala）** 笔记本中 **%%sql**魔术命令的 SparkSQL 查询。 请注意，笔记本的主要语言设置为 Scala。

   ![突触-火花魔法](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用临时表跨语言引用数据

您不能在 Synapse Studio 笔记本中直接跨不同语言引用数据或变量。 在 Spark 中，可以跨语言引用临时表。 下面是如何在 中`Scala``PySpark`读取数据帧和使用`SparkSQL`Spark 临时表作为解决方法的示例。

1. 在单元 1 中，使用 Scala 从 SQL 池连接器读取数据帧并创建临时表。

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

### <a name="ide-style-intellisense"></a>IDE 风格的感知

Azure Synapse 工作室笔记本与摩纳哥编辑器集成，将 IDE 风格的 IntelliSense 引入单元编辑器。 语法突出显示、错误创建器和自动代码完成可帮助您更快地编写代码和识别问题。

IntelliSense 功能在不同的语言中处于不同的成熟度级别。 使用下表查看支持的内容。

|Languages| 语法高光 | 语法错误标记  | 语法代码完成 | 可变代码完成| 系统功能代码完成| 用户功能代码完成| 智能缩进 | 代码折叠|
|--|--|--|--|--|--|--|--|--|
|皮火花（Python）|是|是|是|是|是|是|是|是|
|火花（斯卡拉）|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|Spark.NET （C#）|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>使用工具栏按钮设置文本单元格的格式

您可以使用文本单元格工具栏中的格式按钮执行常见的标记操作。 它包括粗体文本、斜体文本、插入代码段、插入无序列表、插入有序列表和从 URL 插入图像。

  ![突触-文本-单元格-工具栏](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>撤消单元格操作
单击**撤消**按钮或按**Ctrl_Z**撤消最新的单元格操作。 现在，您可以撤消最多最新的 20 个历史单元格操作。 

   ![突触-撤消细胞](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移动单元格

选择椭圆 （...） 以访问最右侧的其他单元格操作菜单。 然后选择**向上移动单元格**或**向下移动单元格**以移动当前单元格。 

您还可以[在命令模式下使用快捷键](#shortcut-keys-under-command-mode)。 按**Ctrl_Alt+** 向上移动当前单元格。 按**Ctrl_Alt+** 可向下移动当前单元格。

   ![移动一个单元格](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>删除单元格

要删除单元格，请选择椭圆 （...） 以访问最右侧的其他单元格操作菜单，然后选择 **"删除单元格**"。 

您还可以[在命令模式下使用快捷键](#shortcut-keys-under-command-mode)。 按**D，D**以删除当前单元格。
  
   ![删除单元格](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>折叠单元格输入
单击当前单元格底部的箭头按钮将其折叠。 要展开它，请在单元格折叠时单击箭头按钮。

   ![折叠单元输入](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>折叠单元格输出

单击当前单元输出左上角的**折叠输出**按钮将其折叠。 要展开它，请在单元格输出折叠时单击 **"显示单元格输出**"。

   ![折叠单元输出](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>运行笔记本

您可以单独或全部运行笔记本中的代码单元格。 笔记本中表示每个单元格的状态和进度。

### <a name="run-a-cell"></a>运行单元格

在单元格中运行代码的方法有多种。

1. 将鼠标悬停在要运行的单元格上，然后选择 **"运行单元格"** 按钮或按**Ctrl_Enter**。

   ![运行单元-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 要访问最右侧的其他单元格操作菜单，请选择椭圆 **（...）。** 然后，选择 **"运行单元格**"。

   ![运行单元-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 在[命令模式下使用快捷键](#shortcut-keys-under-command-mode)。 按**Shift_Enter**可运行当前单元格并选择下面的单元格。 按**Alt_Enter**可运行当前单元格，并在下方插入新单元格。


### <a name="run-all-cells"></a>运行所有单元格
单击"**全部运行"** 按钮以按顺序运行当前笔记本中的所有单元格。

   ![运行全单元](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>运行上面或下方的所有单元格

要访问最右侧的其他单元格操作菜单，请选择椭圆 **（...）。** 然后，选择**上面的"运行单元格**"以按顺序运行当前上方的所有单元格。 选择**下面的"运行单元格**"以按顺序运行当前下的所有单元格。

   ![运行单元格 - 上面或以下](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>单元格状态指示器

单元格下方将显示分步单元格执行状态，以帮助您查看其当前进度。 单元运行完成后，将显示一个执行摘要，其中将显示总持续时间和结束时间，并保留在那里供将来参考。

![单元格状态](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>火花进度指示器

Azure Synapse 工作室笔记本纯基于 Spark。 代码单元在 Spark 池上远程执行。 Spark 作业进度指示器提供实时进度栏，似乎可帮助您了解作业执行状态。


![火花-进度指示器](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>火花会话配置

您可以指定超时持续时间、数量和执行器的大小，以便将其授予**配置会话**中的当前 Spark 会话。 重新启动 Spark 会话使配置更改生效。 清除所有缓存的笔记本变量。

![会话-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>将数据带到笔记本上

您可以加载来自 Azure Blob 存储、Azure 数据湖存储第 2 代和 SQL 池的数据，如下代码示例所示。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>从 Azure 数据存储第 2 代将 CSV 作为 Spark 数据帧读取

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>从 Azure Blob 存储中读取 CSV 作为 Spark 数据帧

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

您可以直接访问主存储帐户中的数据。 不需要提供密钥。 在数据资源管理器中，右键单击文件并选择 **"新建笔记本**"以查看具有数据提取器自动生成的新笔记本。

![数据到单元格](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>可视化笔记本中的数据

### <a name="display"></a>显示（）

提供表格结果视图的选项，用于创建条形图、折线图、饼图、散点图和面积图。 无需编写代码即可可视化数据。 图表可以在**图表选项**中自定义。 

默认情况下 **，%%sql**魔术命令的输出将显示在呈现的表视图中。 您可以在 Spark 数据帧或弹性分布式数据集 （RDD） 函数上调用**display（`<DataFrame name>`** ） 以生成呈现的表视图。

   ![内置图表](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>显示HTML（）

您可以使用**显示HTML（）** 渲染 HTML 或交互式库（如**散景**）。

下图是使用**散景**在地图上绘制字形的示例。

   ![散景示例](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

运行以下示例代码以绘制上面的图像。

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

您可以在工作区中保存单个笔记本或所有笔记本。

1. 要保存对单个笔记本所做的更改，请选择笔记本命令栏上的 **"发布**"按钮。

   ![发布笔记本](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 要将工作区中的所有笔记本保存，请选择工作区命令栏上的"**发布所有**"按钮。 

   ![发布-全部](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在笔记本属性中，可以配置是否在保存时包括单元格输出。

   ![笔记本属性](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>魔法命令
您可以在 Azure Synapse 工作室笔记本中使用您熟悉的 Jupyter 魔术命令。 选中下面的列表，作为当前可用的魔术命令。 在 GitHub 上告诉我们您的用例，以便我们继续构建更多神奇的命令，以满足您的需求。

可用的行魔术： [%lsmagic，](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%时间](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [%时间it](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用的细胞魔法： [%%时间](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [%%时间it，](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%捕获](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)， [%%写文件](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)， [%%sql，](#use-multiple-languages) [%%pyspark，](#use-multiple-languages) [%%火花](#use-multiple-languages)， [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>快捷键

与 Jupyter 笔记本类似，Azure Synapse 工作室笔记本具有模式用户界面。 键盘会根据笔记本单元处于哪种模式执行不同操作。 Synapse Studio 笔记本支持给定代码单元的以下两种模式：命令模式和编辑模式。

1. 当没有提示您键入的文本光标时，单元格处于命令模式。 当单元格处于命令模式时，可以编辑整个笔记本，但不能键入单个单元格。 通过按`ESC`或使用鼠标单击单元格的编辑器区域外部进入命令模式。

   ![命令模式](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 编辑模式由文本光标指示，提示您在编辑器区域键入。 当单元格处于编辑模式时，无法键入单元格。 通过按下`Enter`或使用鼠标单击单元格的编辑器区域进入编辑模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快捷键

使用以下击键快捷方式，您可以更轻松地在 Azure Synapse 笔记本中导航和运行代码。

| 操作 |突触工作室笔记本快捷方式  |
|--|--|
|运行当前单元格并选择以下内容 | Shift+Enter |
|运行当前单元格并在下方插入 | Alt+Enter |
|在上面选择单元格| 向上 |
|选择下面的单元格| 向下 |
|在上面插入单元格| A |
|在下面插入单元格| B |
|将所选单元格扩展到上面| Shift + 向上键 |
|在下面扩展所选单元格| Shift + 向下键|
|向上移动单元格| Ctrl_Alt__ |
|向下移动单元格| Ctrl_Alt_ |
|删除所选单元格| D， D |
|切换到编辑模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>编辑模式下的快捷键

使用以下击键快捷方式，在"编辑"模式下，您可以更轻松地在 Azure 同步笔记本中导航和运行代码。

| 操作 |突触工作室笔记本快捷方式  |
|--|--|
|向上移动光标 | 向上 |
|向下移动光标|向下|
|撤消|Ctrl + Z|
|重做|Ctrl + Y|
|注释/取消注释|Ctrl = /|
|删除之前的单词|Ctrl = 后空间|
|删除单词后|Ctrl = 删除|
|转到单元格启动|Ctrl + Home|
|转到单元格端 |Ctrl + End|
|向左走一个字|Ctrl = 左侧|
|向右走一个字|Ctrl = 右侧|
|全选|Ctrl + A|
|降级| Ctrl ||
|缩进|Ctrl ||
|切换到命令模式| Esc |

## <a name="next-steps"></a>后续步骤

- [阿帕奇火花文档的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
