---
title: "在 Azure 机器学习服务（预览版）中为鸢尾花分类教程准备数据 | Microsoft Docs"
description: "本完整教程端到端演示如何使用 Azure 机器学习服务（预览版）。 这是第一部分，讨论数据准备。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: 4e558518a5a1fb7b4cd0a58fe2453fd4c083b46a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>鸢尾花分类（第 1 部分）：准备数据
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是由三个部分构成的系列教程的第一部分。 本教程逐步讲解 Azure 机器学习服务（预览版）的基础知识。 学习如何：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中创建项目。
> * 创建数据准备包。
> * 生成可调用数据准备包的 Python/PySpark 代码。

本教程使用了永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 屏幕截图与 Windows 相关，但 Mac OS 上的体验几乎相同。

## <a name="prerequisites"></a>先决条件
- 创建 Azure 机器学习试验帐户。
- 安装 Azure Machine Learning Workbench。

可以遵循[安装和创建快速入门](quickstart-installation.md)一文中的说明来安装 Azure Machine Learning Workbench 应用程序。 此安装还包括 Azure 跨平台命令行工具，即 Azure CLI。

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>在 Azure Machine Learning Workbench 中创建新项目
1. 打开 Azure Machine Learning Workbench 应用，并根据需要登录。 在“项目”窗格中选择加号 (+) ，创建新项目。

   ![新建工作区](media/tutorial-classifying-iris/new_ws.png)

2. 在“创建新项目”中填写详细信息： 

   ![新建项目](media/tutorial-classifying-iris/new_project.png)

   - 在“项目名称”框中填写项目的名称。 例如，使用值 **myIris**。
   - 选择在其中创建项目的“项目目录”。 例如，使用值 `C:\Temp\`。 
   - 输入可选的“项目说明”。 
   - “Git 存储库”字段也是可选的，可以留空。 可以提供 Visual Studio Team Services 上的现有空 Git 存储库（不带主分支的存储库）。 如果使用已存在的 Git 存储库，可以在以后启用漫游和共享方案。 有关详细信息，请参阅[使用 Git 存储库](using-git-ml-project.md)。 
   - 选择一个工作区，例如，本教程使用 IrisGarden。 
   - 从项目模板列表中选择“鸢尾花分类”模板。 

3. 选择“创建”按钮。 现在会创建并打开一个项目。

## <a name="create-a-data-preparation-package"></a>创建数据准备包
1. 从“文件视图”打开 **iris.csv** 文件。 该文件是一个表，包含 5 列和 150 行。 其中有四个数字特征列和一个字符串目标列。 该表不带列标题。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 不要在项目文件夹中包含数据文件，尤其是文件大小较大时。 为了演示，我们在此模板中包含了 iris.csv，因为该文件较小。 有关详细信息，请参阅[如何读取和写入大型数据文件](how-to-read-write-files.md)。

2. 在“数据视图”中选择加号 (+) ，添加新的数据源。 此时会打开“添加数据源”页。 

   ![数据视图](media/tutorial-classifying-iris/data_view.png)

3. 选择“文本文件(*.csv、.json、.txt....)”，单击“下一步”。
   ![数据源](media/tutorial-classifying-iris/data-source.png)
   

4. 浏览到文件 **iris.csv**，然后单击“下一步”。  
 
   ![选择 iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >确保从本练习的当前项目目录内部选择 iris.csv 文件， 否则后续步骤可能失败。
   
5. 保留默认值，并单击“完成”。

6. 此时会创建名为 iris-1.dsource 的新文件。 使用短划线“-1”为该文件进行唯一的命名，因为示例项目附带了没有编号的 iris.dsource 文件。  

   此时会打开文件并显示数据。 “列 1”到“列 5”的一系列列标题会自动添加到此数据集。 滚动到底部。可以看到，数据集的最后一行是空的。 该行为空是因为 CSV 文件中有一个额外的换行符。

   ![Iris 数据视图](media/tutorial-classifying-iris/iris_data_view.png)

7. 选择“指标”按钮。 观察直方图。 已为每一列计算出一组完整的统计信息。 还可以选择“数据”按钮来再次查看数据。 

   ![Iris 数据视图](media/tutorial-classifying-iris/iris_metrics_view.png)

8. 选择“准备”按钮。 此时会打开“准备”对话框。 

   示例项目附带 iris.dprep 文件。 默认情况下，它会要求你在已经存在的 iris.dprep 数据准备包中创建一个新的数据流。 

   从下拉菜单中选择“+ 新建数据准备包”，输入新值作为包名称，使用 iris-1，然后选择“确定”。

   ![Iris 数据视图](media/tutorial-classifying-iris/new_dprep.png)

   随后会在数据准备编辑器中创建并打开名为 iris-1.dprep 的新数据准备包。

9. 现在，让我们进行一些基本的数据准备工作。 将列重命名。 选择每个列标题，使标题文本可编辑。 

   分别在五个列中输入“花萼长度”、“花萼宽度”、“花瓣长度”、“花瓣宽度”和“物种”。

   ![将列重命名](media/tutorial-classifying-iris/rename_column.png)

10. 若要统计非重复值，请选择“物种”列，然后右键单击它进行选择。 从下拉菜单中选择“值计数”。 

   ![选择“值计数”](media/tutorial-classifying-iris/value_count.png)

   此操作会打开“检查器”窗格并显示包含四个条形的直方图。 目标列包含三个非重复值：Iris_virginica、Iris_versicolor、Iris-setosa，以及一个 (null) 值。

11. 若要筛选出 null 值，请在图形中选择表示 null 值的条形。 有一个包含 **(null)** 值的行。 若要删除此行，请选择减号 (-)。

   ![值计数直方图](media/tutorial-classifying-iris/filter_out.png)

12. 注意“步骤”窗格中详述的各个步骤。 在重命名列并筛选 null 值行时，系统将每个操作作为数据准备步骤记录。 可以编辑各个步骤以调整设置、为步骤重新排序，以及删除步骤。

   ![步骤](media/tutorial-classifying-iris/steps.png)

13. 关闭数据准备编辑器。 在带图标的“iris-1”选项卡上选择“关闭”(x)。 工作会自动保存到“数据准备”标题下显示的 iris-1.dprep 文件中。

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>生成可调用数据准备包的 Python/PySpark 代码

1. 右键单击 iris 1.dprep 文件打开上下文菜单，然后选择“生成数据访问代码文件”。 

   ![生成代码](media/tutorial-classifying-iris/generate_code.png)

2. 此时会打开包含以下代码行的名为 iris-1.py 的新文件：

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

   此代码片段调用作为数据准备包创建的逻辑。 `df` 表示各种类型的数据帧，具体取决于此代码的运行上下文。 在 Python 运行时中执行时，使用 [pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)；在 Spark 上下文中执行时，使用 [Spark 数据帧](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 

   有关如何在 Azure Machine Learning Workbench 中准备数据的详细信息，请参阅[数据准备入门](data-prep-getting-started.md)指南。

## <a name="next-steps"></a>后续步骤
在由三个部分构成的系列教程的此第一部分，已使用 Azure Machine Learning Workbench 执行了以下操作：
> [!div class="checklist"]
> * 创建新项目。 
> * 创建数据准备包。
> * 生成可调用数据准备包的 Python/PySpark 代码。

现在可以转到此系列教程的下一部分，了解如何生成 Azure 机器学习模型：
> [!div class="nextstepaction"]
> [生成模型](tutorial-classifying-iris-part-2.md)
