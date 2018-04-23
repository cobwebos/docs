---
title: 在 Azure 机器学习服务（预览版）中为鸢尾花分类教程准备数据 | Microsoft Docs
description: 本教程的完整内容包括关于如何使用 Azure 机器学习服务（预览版）的端到端演示。 这是第一部分，讨论数据准备。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 7532e252638832ab6257d2edc97c98a03eef6316
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>教程 1：鸢尾花分类 - 准备数据

Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是**由三个部分构成的系列教程的第一部分**。 本教程逐步讲解 Azure 机器学习服务（预览版）的基础知识，以及如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中创建项目
> * 创建数据准备包
> * 生成可调用数据准备包的 Python/PySpark 代码

本教程使用了历久弥新的[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 

## <a name="prerequisites"></a>先决条件

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要完成本教程，必须满足以下先决条件：
- 一个 Azure 机器学习试验帐户
- 已安装 Azure Machine Learning Workbench

如果尚不具备这两个先决条件，请按[快速入门：安装和启动](../service/quickstart-installation.md)一文中的步骤设置帐户并安装 Azure Machine Learning Workbench 应用程序。 

## <a name="create-a-new-project-in-workbench"></a>在 Workbench 中创建新项目

如果已按[快速入门：安装和启动](../service/quickstart-installation.md)一文中的步骤操作，则应该已经有了该项目，可以跳到下一部分。

1. 打开 Azure Machine Learning Workbench 应用，并根据需要登录。 
   
   + 在 Windows 上，请使用 **Machine Learning Workbench** 桌面快捷方式来启动它。 
   + 在 macOS 上，请在 Launchpad 中选择“Azure ML Workbench”。

1. 在“项目”窗格中选择加号 (+)，然后选择“新建项目”。  

   ![新建工作区](./media/tutorial-classifying-iris/new_ws.png)

1. 填充各窗体字段，然后选择“创建”按钮在 Workbench 中创建新项目。

   字段|针对教程建议的值|说明
   ---|---|---
   项目名称 | myIris |输入用于标识帐户的唯一名称。 可以使用自己的名称，或者能够最好地标识试验的部门或项目名称。 名称应介于 2 到 32 个字符之间， 只应包含字母数字字符和短划线 (-) 字符。 
   项目目录 | c:\Temp\ | 指定在其中创建项目的目录。
   项目说明 | 留空 | 用于描述项目的可选字段。
   Visualstudio.com GIT 存储库 URL |留空 | 可选字段。 可以在 Visual Studio Team Services 中将项目与 Git 存储库关联，以便进行源代码管理和协作。 [了解如何进行该设置](https://docs.microsoft.com/en-us/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo)。 
   所选工作区 | IrisGarden（如果存在） | 在 Azure 门户中选择一个已经为试验帐户创建的工作区。 <br/>如果是按本快速入门进行的操作，则应该已经有了一个名为 IrisGarden 的工作区。 如果还没有该工作区，则请选择一个在创建试验帐户时创建的工作区，或者任何其他需要使用的工作区。
   项目模板 | 鸢尾花分类 | 模板包含的脚本和数据可以用来探索产品的功能。 此模板包含的脚本和数据是本快速入门以及此文档站点中的其他教程所需要的。 

   ![新建项目](media/tutorial-classifying-iris/new_project.png)
 
 此时会创建新的项目，并会打开包含该项目的项目仪表板。 此时，可以浏览项目主页、数据源、Notebook 和源代码文件。 

   ![打开项目](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>创建数据准备包

接下来即可在 Azure Machine Learning Workbench 中进行浏览，并开始准备数据。 在 Workbench 中执行的每次转换都以 JSON 格式存储在本地数据准备包（*.dprep 文件）中。 此数据准备包是存储 Workbench 中的数据准备工作的主要容器。

此数据准备包可以稍后传递给运行时，例如 local-C#/CoreCLR、Scala/Spark 或 Scala/HDI。 

1. 选择文件夹图标来打开“文件”视图，然后选择 **iris.csv** 来打开该文件。

   该文件包含一个表，表中有 5 列和 50 行。 四个列是数字特征列。 第五列是字符串目标列。 没有任何列有标头名称。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 不要在项目文件夹中包含数据文件，尤其是文件较大时。 **iris.csv** 数据文件很小，因此将其包括在此模板中进行演示。 有关详细信息，请参阅[如何读取和写入大型数据文件](how-to-read-write-files.md)。

2. 在“数据视图”中选择加号 (+) ，添加新的数据源。 此时会打开“添加数据源”页。 

   ![Azure Machine Learning Workbench 中的数据视图](media/tutorial-classifying-iris/data_view.png)

3. 选择“文本文件(\*.csv、\*.json、\*.txt...)”，然后单击“下一步”。
   ![Azure Machine Learning Workbench 中的数据源](media/tutorial-classifying-iris/data-source.png)

4. 浏览到 **iris.csv** 文件，然后单击“完成”。 这将使用参数（例如分隔符和数据类型）的默认值。

   >[!IMPORTANT]
   >确保从本练习的当前项目目录内部选择 iris.csv 文件， 否则后续步骤可能失败。
 
   ![选择 iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. 此时会创建名为 iris-1.dsource 的新文件。 使用短划线“-1”为该文件进行唯一的命名，因为示例项目附带了没有编号的 iris.dsource 文件。  

   此时会打开文件并显示数据。 “列 1”到“列 5”的一系列列标题会自动添加到此数据集。 滚动到底部。可以看到，数据集的最后一行是空的。 该行为空是因为 CSV 文件中有一个额外的换行符。

   ![Iris 数据视图](media/tutorial-classifying-iris/iris_data_view.png)

1. 选择“指标”按钮。 此时会生成并显示直方图。

   可以通过选择“数据”按钮切换回数据视图。
   
   ![Iris 数据视图](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. 观察直方图。 已为每一列计算出一组完整的统计信息。 

   ![Iris 数据视图](media/tutorial-classifying-iris/iris_metrics_view.png)

8. 选择“准备”按钮，开始创建数据准备包。 此时会打开“准备”对话框。 

   示例项目包含一个默认选中的 **iris.dprep** 数据准备 文件。 

   ![Iris 数据视图](media/tutorial-classifying-iris/prepare.png)

1. 从下拉菜单中选择“+ 新建数据准备包”，创建一个新的数据准备包。

   ![Iris 数据视图](media/tutorial-classifying-iris/prepare_new.png)

1. 输入一个新值作为包名称（使用 **iris-1**），然后选择“确定”。

   此时会创建并在数据准备编辑器中打开名为 iris-1.dprep 的新数据准备包。

   ![Iris 数据视图](media/tutorial-classifying-iris/prepare_iris-1.png)

   现在，让我们进行一些基本的数据准备工作。 

1. 选择每个列标题，使标题文本可编辑。 然后重命名每个列，如下所示： 

   按顺序分别在五个列中输入“花萼长度”、“花萼宽度”、“花瓣长度”、“花瓣宽度”和“物种”。

   ![将列重命名](media/tutorial-classifying-iris/rename_column.png)

1. 对非重复值计数：
   1. 选择“物种”列
   1. 右键单击将其选中。 
   1. 从下拉菜单中选择“值计数”。 

   此时会打开数据下面的“检查器”窗格。 会显示包含四个条形的直方图。 目标列包含四个非重复值：Iris-virginica、Iris-versicolor、Iris-setosa，以及一个 (null) 值。

   ![选择“值计数”](media/tutorial-classifying-iris/value_count.png)

   ![值计数直方图](media/tutorial-classifying-iris/filter_out.png)

1. 若要筛选掉 null 值，请选择“(null)”栏，然后选择减号 (**-**)。 

   然后，(null) 行变成灰色，表示它已被筛选掉。 

   ![筛选掉 null](media/tutorial-classifying-iris/filter_out2.png)

1. 注意“步骤”窗格中详述的各个数据准备步骤。 在重命名列并筛选 null 值行时，系统将每个操作作为数据准备步骤记录。 可以编辑各个步骤以调整其设置、为步骤重新排序，以及删除步骤。

   ![步骤](media/tutorial-classifying-iris/steps.png)

1. 关闭数据准备编辑器。 选择带图标的“iris-1”选项卡上的 x 图标，将该选项卡关闭。工作会自动保存到“数据准备”标题下显示的 iris-1.dprep 文件中。

   ![关闭](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>生成可调用数据准备包的 Python/PySpark 代码

 可以直接在 Python 或 Jupyter Notebook 中浏览数据准备包的输出。 可以跨多个运行时执行包，包括本地 Python、Spark（包含在 Docker 中），以及 HDInsight。 

1. 在“数据准备”选项卡下找到 **iris-1.dprep** 文件。

1. 右键单击 **iris-1.dprep** 文件，然后从上下文菜单中选择“生成数据访问代码文件”。 

   ![生成代码](media/tutorial-classifying-iris/generate_code.png)

   此时会打开名为 **iris-1.py** 的新文件，其中包含以下代码行，用于调用以数据准备包形式创建的逻辑：

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   `df` 表示另一类 DataFrame，具体取决于此代码的运行上下文：
   + 在 Python 运行时中执行时，使用 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。
   + 在 Spark 上下文中执行时，使用 [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 
   
   若要详细了解如何在 Azure Machine Learning Workbench 中准备数据，请参阅[数据准备入门](data-prep-getting-started.md)指南。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Azure Machine Learning Workbench 执行以下操作：
> [!div class="checklist"]
> * 创建新项目
> * 创建数据准备包
> * 生成可调用数据准备包的 Python/PySpark 代码

现在可以转到此教程系列的下一部分，了解如何生成 Azure 机器学习模型：
> [!div class="nextstepaction"]
> [教程 2 - 生成模型](tutorial-classifying-iris-part-2.md)
