---
title: 教程 - 适用于 VSCode 的 Spark & Hive Tools（Spark 应用程序）
description: 教程 - 使用适用于 VSCode 的 Spark & Hive Tools 开发以 Python 编写的 Spark 应用程序，并将其提交到 Apache Spark 池（预览版）。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: 6778d78ff5e342d97c1c9bc477c1a88eca42a10a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91337641"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>教程：使用 Synapse 工作区通过 VSCode 创建 Apache Spark 应用程序

了解如何使用适用于 Visual Studio Code 的 Apache Spark 和 Hive 工具。 使用工具为 Apache Spark 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和 PySpark 脚本。 首先，本文将介绍如何在 Visual Studio Code 中安装 Spark 和 Hive 工具。 随后介绍如何将作业提交到 Spark 和 Hive 工具。

可将 Spark 和 Hive 工具安装在 Visual Studio Code 支持的平台上。 注意针对不同平台的以下先决条件。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下各项：

- Apache Spark 池。 若要创建 Apache Spark 池，请参阅[使用 Azure 门户创建 Apache Spark 池](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。
- [Visual Studio Code 的 PySpark 交互式环境](../../hdinsight/set-up-pyspark-interactive-environment.md)。
- 本地目录。 本文使用 C:\HD\Synaseexample。

## <a name="install-spark--hive-tools"></a>安装 Spark 和 Hive 工具

满足先决条件后，可遵循以下步骤安装适用于 Visual Studio Code 的 Spark 和 Hive 工具：

1. 打开 Visual Studio Code。

2. 从菜单栏中，导航到“查看” > “扩展” 。

3. 在搜索框中，输入“Spark 和 Hive”。

4. 从搜索结果中选择“Spark 和 Hive 工具”，然后选择“安装”： 

     ![适用于 Visual Studio Code 的 Spark 和 Hive Python 安装](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. 根据需要选择“重载”。

## <a name="open-a-work-folder"></a>打开工作文件夹

若要打开工作文件夹并在 Visual Studio Code 中创建文件，请执行以下步骤：

1. 从菜单栏中，导航到“文件” > “打开文件夹...” > “C:\HD\Synaseexample”，然后选择“选择文件夹”按钮   。 该文件夹显示在左侧的“资源管理器”视图中。

2. 在“资源管理器”视图中，选择“Synaseexample”文件夹，然后选择工作文件夹旁边的“新建文件”图标  ：

     ![visual studio code“新建文件”图标](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. 使用 `.py`（Spark 脚本）文件扩展名命名新文件。 此示例使用 HelloWorld.py****。

## <a name="connect-to-your-spark-pools"></a>连接到 Spark 池

登录到 Azure 订阅以连接到 Spark 池。

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

遵循以下步骤连接到 Azure：

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入“Azure:  登录”：

     ![适用于 Visual Studio Code 的 Spark 和 Hive 工具登录](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. 按照登录说明登录到 Azure。 连接后，Visual Studio Code 窗口底部的状态栏上会显示 Azure 帐户名称。

## <a name="set-the-default-spark-pool"></a>设置默认 Spark 池

1. 重新打开[前面](#open-a-work-folder)讨论的 Synaseexample 文件夹（如果已关闭）。  

2. 选择[前面](#open-a-work-folder)创建的 HelloWorld.py 文件。 它将在脚本编辑器中打开。

3. 右键单击脚本编辑器，然后选择“Synapse:设置默认 Spark 池”。  

4. [连接](#connect-to-your-spark-pools)到 Azure 帐户（如果尚未这样做）。

5. 选择一个 Spark 池作为当前脚本文件的默认 Spark 池。 工具将自动更新 **.VSCode\settings.json** 配置文件：

     ![设置默认群集配置](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>将交互式 Synapse PySpark 查询提交到 Spark 池

用户可以通过以下方式在 Spark 池上执行 Synapse PySpark Interactive：

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>在 PY 文件中使用 Synapse PySpark Interactive 命令
使用 PySpark Interactive 命令提交查询时，请执行以下步骤：

1. 重新打开[前面](#open-a-work-folder)讨论的 Synaseexample 文件夹（如果已关闭）。  

2. 遵循[前面](#open-a-work-folder)所述的步骤创建新的 **HelloWorld.py** 文件。

3. 将以下代码复制并粘贴到脚本文件中：

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. 窗口右下角显示安装 PySpark/Synapse Pyspark 内核的提示。 可以单击“安装”按钮继续安装 PySpark/Synapse Pyspark；或单击“跳过”按钮跳过此步骤。

     ![安装 PySpark 内核](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. 如果以后需要安装它，可以导航到“文件” > “首选项” > “设置”，然后在设置中取消选中“Hdinsight:允许跳过 Pyspark 安装”。 
    
     ![允许跳过 pyspark 安装](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. 如果在步骤 4 中安装成功，则会在窗口右下角显示“PySpark/Synapse Pyspark 安装成功”消息框。 单击“重载”按钮可重载此窗口。

     ![已成功安装 PySpark](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. 从菜单栏中，导航到“查看” > “命令面板...”或使用 Shift + Ctrl + P 键盘快捷键，然后输入“Python:选择解释器以启动 Jupyter 服务器”。

     ![选择解释器以启动 jupyter 服务器](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. 选择下面的 python 选项。

     ![选择下面的选项](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. 从菜单栏中，导航到“查看” > “命令面板...”或使用 Shift + Ctrl + P 键盘快捷键，然后输入“开发人员:重载窗口”。

     ![重载窗口](./media/vscode-tool-synapse/reload-window.png)

10. [连接](#connect-to-your-spark-pools)到 Azure 帐户（如果尚未这样做）。

11. 选择所有代码，右键单击脚本编辑器，然后选择“Synapse:Pyspark Interactive”以提交查询。 

     ![Pyspark Interactive 上下文菜单](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. 如果尚未指定默认 Spark 池，请选择 Spark 池。 几分钟后，“Python Interactive”结果将显示在新选项卡中。单击 PySpark 将内核切换为 Synapse PySpark，然后再次提交所选代码，该代码将成功运行。 工具还允许使用上下文菜单提交代码块而非整个脚本文件：

     ![交互式](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>使用 #%% 注释在 PY 文件中执行交互式查询

1. 在代码前添加 **#%%** ，以获取笔记本体验。

     ![添加 #%%](./media/vscode-tool-synapse/run-cell.png)

2. 单击“运行单元格”。 几分钟后，Python Interactive 结果将显示在新选项卡中。单击 PySpark 将内核切换为 Synapse PySpark，然后再次单击“运行单元格”，代码将成功运行。 

     ![“运行单元格”命令的结果](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>利用 Python 扩展中的 IPYNB 支持

1. 若要创建 Jupyter Notebook，可以使用命令面板中的命令，也可以在工作区中创建新的 .ipynb 文件。 有关详细信息，请参阅[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)

2. 单击“运行单元格”按钮，按提示“设置默认 Spark 池”（强烈建议每次打开笔记本之前都设置默认群集/池），然后重载窗口。

     ![设置默认 spark 池并重载](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. 单击 PySpark 将内核切换为 Synapse Pyspark，然后单击“运行单元格”，一段时间后将显示结果。

     ![运行 ipynb 的结果](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. 此扩展不支持 2020.5.78807 以上（含）的 Ms-python 版本，这是一个[已知问题](#known-issues)。
>  
>2. 切换到 Synapse Pyspark 内核，建议在 Azure 门户中禁用自动设置。 否则，首次使用时，可能需要很长时间才能唤醒群集和设置 synapse 内核。 
>
>    ![自动设置](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>将 PySpark Batch 作业提交到 Spark 池

1. 重新打开[前面](#open-a-work-folder)讨论的 Synaseexample 文件夹（如果已关闭）。  

2. 遵循[前面](#open-a-work-folder)所述的步骤创建新的 **BatchFile.py** 文件。

3. 将以下代码复制并粘贴到脚本文件中：

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [连接](#connect-to-your-spark-pools)到 Azure 帐户（如果尚未这样做）。

5. 右键单击脚本编辑器，然后选择“Synapse:PySpark Batch”。

6. 选择要将 PySpark 作业提交到的 Spark 池：

     ![提交 Python 作业结果输出](./media/vscode-tool-synapse/submit-pythonjob-result.png)

将 Batch 作业提交到 Spark 池后，提交日志将显示在 Visual Studio Code 的“输出”窗口中。 该窗口还会显示 Spark UI URL 和 Spark 作业应用程序 UI URL。 你可以在 Web 浏览器中打开 URL 以跟踪作业状态。

## <a name="access-and-manage-synapse-workspace"></a>访问和管理 Synapse 工作区

可以在适用于 VSCode 的 Spark & Hive Tools 内部的 Azure 资源管理器中执行不同的操作。 在 Azure 资源管理器中。

![azure 图像](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>启动工作区

1. 在 Azure 资源管理器中，导航到“SYNAPSE”，将其展开以显示 Synapse 订阅列表。

     ![synapse 资源管理器](./media/vscode-tool-synapse/synapse-explorer.png)

2. 单击 Synapse 订阅工作区，将其展开以显示工作区列表。

3. 右键单击工作区，然后选择“查看 Apache Spark 应用程序”，系统将打开 Synapse Studio 网站中的 Apache Spark 应用程序页面。

     ![右键单击工作区](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![synapse studio 应用程序](./media/vscode-tool-synapse/synapse-studio-application.png)

4. 展开工作区，将显示“默认存储”和“Spark 池”。

5. 右键单击“默认存储”，将显示“复制完整路径”和“在 Synapse Studio 中打开”。 

     ![右键单击默认存储](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - 单击“复制完整路径”，将复制主 ADLS Gen2 帐户 URL，你可以将其粘贴到所需位置。

     - 单击“在 Synapse Studio 中打开”，将在 Synapse Studio 中打开主存储帐户。

     ![synapse studio 中的默认存储](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. 展开“默认存储”，将显示主存储帐户。

7. 展开“Spark 池”，将显示工作区中的所有 Spark 池。

     ![展开存储池](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>已知问题

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>此扩展不支持 2020.5.78807 以上（含）的 ms-python 版本 

“无法连接到 Jupyter 笔记本。” 是 2020.5.78807 以上（含）的 python 版本的一个已知问题。 建议用户使用 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** 版 ms-python 以避免此问题。

![已知问题](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](../overview-what-is.md)
- [为 Azure Synapse Analytics 工作区创建新的 Apache Spark 池](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
