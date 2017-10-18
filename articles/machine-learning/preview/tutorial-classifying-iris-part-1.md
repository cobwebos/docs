---
title: "在机器学习服务（预览版）中为鸢尾花分类教程准备数据 | Microsoft Docs"
description: "本完整教程端到端演示如何使用 Azure 机器学习服务（预览版）。 本文是有关数据准备的教程的第 1 部分。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>鸢尾花分类教程第 1 部分：准备数据
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是由三个部分构成的系列教程的第一部分。 本教程逐步讲解 Azure 机器学习服务（预览）的基础知识。 本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中创建项目
> * 创建数据准备包
> * 生成 Python/PySpark 代码来调用数据准备包

为方便起见，本教程使用了永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 屏幕截图与 Windows 相关，但 macOS 上的体验几乎相同。

## <a name="prerequisites"></a>先决条件
- 创建 Azure 机器学习试验帐户
- 安装 Azure Machine Learning Workbench

可以遵循[安装和创建快速入门](quickstart-installation.md)安装 Azure Machine Learning Workbench 应用程序。 该项安装还包括命令行接口 (CLI)。

## <a name="create-a-new-project-in-azure-ml-workbench"></a>在 Azure ML Workbench 中创建新项目
1. 启动 Azure Machine Learning Workbench 应用程序，并根据需要登录。 在“项目”窗格中，单击 **+** 图标创建**新项目**。

   ![新工作区](media/tutorial-classifying-iris/new_ws.png)

2. 在“创建新项目”中填写详细信息。 

   ![新建项目](media/tutorial-classifying-iris/new_project.png)

   - 在“项目名称”字段中填写项目的名称。 例如，使用值 **myIris**。
   - 选择要在其中创建项目的“项目目录”。 例如，使用值 **C:\Temp**。 
   - 在“项目说明”中输入说明。 
   - “Git 存储库”字段是可选的，可以留空。 可以提供 VSTS 上的现有空 Git 存储库（不带主分支的存储库）。 这样，稍后就可以启用漫游和共享方案。 有关详细信息，请参阅[使用 Git 存储库](using-git-ml-project.md)一文。 
   - 选择一个**工作区**，例如，本教程使用 **IrisGarden**。 
   - 从项目模板列表中选择“鸢尾花分类”模板。 

3. 单击“创建”按钮创建项目。 现在会创建并打开一个项目。

## <a name="create-a-data-preparation-package"></a>创建数据准备包
1. 从“文件视图”打开 **iris.csv** 文件。 该文件是一个简单的表，包含 5 列和 150 行。 其中有四个数字特征列和一个字符串目标列。 该表不带列标题。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 建议不要在项目文件夹中包含数据文件，尤其是文件大小较大时。 为了演示，我们在此模板中包含了 **iris.csv**，因为该文件较小。 有关详细信息，请参阅[如何读取和写入大型数据文件](how-to-read-write-files.md)一文。

2. 在“数据视图”中，单击 **+** 图标添加新的数据源。 随后会启动“添加数据源”向导。 

   ![数据视图](media/tutorial-classifying-iris/data_view.png)

3. 完成数据准备向导。 
   - 在第一个屏幕上，选择“文件/目录”选项，单击“下一步”。
   - 在第二个屏幕上，选择 **iris.csv** 本地文件，例如“C:\Temp\myIris\iris.csv”。
   - 在第三个屏幕上，选择“文件详细信息”并接受默认值。
   - 在第四个屏幕上，选择“数据类型”，将“列 1”到“列 4”的“数据类型”从“字符串”更改为“数字”，因为这些列包含数字值。 
   - 在第五和第六个屏幕上，接受默认设置。
   - 单击“完成”按钮。

   ![选择 iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >确保从本练习的当前项目目录内部选择 **iris.csv** 文件，否则后续步骤可能失败。 

4. 随后会创建新文件 **iris-1.dsource**。 已使用单短划线将该文件唯一命名，因为示例项目已附带了没有编号的 **iris.dsource** 文件。  

   文件将会打开，数据将会显示。 “列 1”到“列 5”的一系列列标题会自动添加到此数据集。 滚动到底部，可以看到，数据集的最后一行是空的， 因为它是 csv 文件中一个额外的换行符。

   ![iris 数据视图](media/tutorial-classifying-iris/iris_data_view.png)

5. 现在单击“指标”按钮。 观察直方图，以及系统针对每个列自动计算的整套统计信息。 还可以单击“数据”按钮再次查看数据。 

   ![iris 数据视图](media/tutorial-classifying-iris/iris_metrics_view.png)

6. 单击“准备”按钮。 此时会弹出“准备”对话框。 

   示例项目附带一个 **iris.dprep** 文件，因此，默认情况下，它会要求在该现有 **iris.dprep** 数据准备包中创建新的数据流。 

   将下拉列表值更改为“+新建数据准备包”，输入新值“iris-1”，单击“确定”。

   ![Iris 数据视图](media/tutorial-classifying-iris/new_dprep.png)

   随后会在数据准备编辑器中创建并打开名为 **iris-1.dprep** 的新数据准备包。

7. 现在，让我们进行一些基本的数据准备工作。 单击每个列标题重命名列，并使标题文本可编辑。 

   分别在五个列中输入“花萼长度”、“花萼宽度”、“花瓣长度”、“花瓣宽度”和“物种”。

   ![重命名列](media/tutorial-classifying-iris/rename_column.png)

8. 若要统计非重复值，请选择并右键单击“物种”列。 选择“值计数”。 

   ![单击“值计数”](media/tutorial-classifying-iris/value_count.png)

   此操作会打开“检查器”窗格并显示包含四个条形的直方图。 请注意，目标列包含三个非重复值：**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** 和 **(null)** 值。

9. 在图形中选择表示 null 值的条形，筛选出 null 值。 有一个包含 **(null)** 值的行。 若要消除此行，请单击 **-** 筛选按钮。

   ![值计数直方图](media/tutorial-classifying-iris/filter_out.png)

10. 注意“步骤”窗格中详述的各个步骤。 在处理列重命名和筛选 null 值行的过程中，每个操作已记录为数据准备步骤。 可以编辑各个步骤以调整设置、为步骤重新排序，甚至删除步骤。

   ![步骤](media/tutorial-classifying-iris/steps.png)

11. 现在，单击名为 **iris-1** 的选项卡上的“X”，通过图形图标关闭数据准备编辑器。 工作会自动保存到“数据准备”标题下显示的 **iris-1.dprep** 文件中。

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>生成 Python/PySpark 代码来调用数据准备包

1. 右键单击 **iris 1.dprep** 文件打开上下文菜单，选择“生成数据访问代码文件”。 

   ![生成代码](media/tutorial-classifying-iris/generate_code.png)

2. 此时会打开包含以下代码行的名为 **iris-1.py** 的新文件：

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   此代码片段调用作为数据准备包创建的逻辑。 根据此代码的运行上下文，`df` 表示不同类型的数据帧。 在 Python 运行时中执行时，使用 [pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)；在 Spark 上下文中执行时，使用 [Spark 数据帧](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 

   有关如何在 Azure Machine Learning Workbench 中准备数据的详细信息，请参阅[数据准备入门](data-prep-getting-started.md)指南。

## <a name="next-steps"></a>后续步骤
在由三个部分构成的系列教程的本第一部分中，我们已使用 Azure Machine Learning Workbench 执行了以下操作：
> [!div class="checklist"]
> * 创建新项目 
> * 创建数据准备包
> * 生成 Python/PySpark 代码来调用数据准备包

现在，可以转到系列教程的下一部分来生成机器学习模型。
> [!div class="nextstepaction"]
> [生成模型](tutorial-classifying-iris-part-2.md)
