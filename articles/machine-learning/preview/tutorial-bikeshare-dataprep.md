---
title: "共享自行车教程 - 使用 Azure Machine Learning Workbench 执行高级数据准备"
description: "有关使用 Azure Machine Learning Workbench 的端到端数据准备教程"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: 9e20c606973447e0b01eaf9716fabf47eefd228b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>共享自行车教程：使用 Azure Machine Learning Workbench 执行高级数据准备
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

在本教程中，将使用 Azure 机器学习服务（预览版）来了解如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure 机器学习数据准备工具以交互方式准备数据
> * 导入、转换和创建测试数据集
> * 生成数据准备包
> * 使用 Python 运行数据准备包
> * 通过将数据准备包重复用于更多输入文件来生成训练数据集

> [!IMPORTANT]
> 本教程仅准备数据，它不构建预测模型。
>
> 可以使用准备好的数据来训练你自己的预测模型。 例如，可以创建一个模型来预测在 2-小时时间窗口内的自行车需求。

## <a name="prerequisites"></a>先决条件
* 需要在本地安装 Azure Machine Learning Workbench。 有关详细信息，请参阅[安装快速入门](quickstart-installation.md)。
* 熟悉在 Workbench 中创建新项目。

## <a name="data-acquisition"></a>数据获取
本教程使用[波士顿 Hubway 数据集](https://s3.amazonaws.com/hubway-data/index.html)和来自 [NOAA](http://www.noaa.gov/) 的波士顿天气数据。

1. 从下列链接将数据文件下载到你的本地开发环境。 
   * [波士顿天气数据](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)。 
   * 来自 Hubway 网站的 Hubway 行程数据。

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. 在下载后解压缩每个 .zip 文件。

## <a name="learn-about-the-datasets"></a>了解数据集
1. __波士顿天气__文件包含与天气相关的下列字段，这些字段每小时报告一次：
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. __Hubway__ 数据按年份和月份组织到各个文件中。 例如，名为 `201501-hubway-tripdata.zip` 的文件包含一个 .csv 文件，其中包含了 2015 年 1 月份的数据。 该数据包含下列字段，每行表示一次自行车行程：

   * 行程持续时间（以秒为单位）
   * 开始时间和日期
   * 停止时间和日期
   * 起始站名和 ID
   * 结束站名和 ID
   * 自行车 ID
   * 用户类型（非正式 = 24-小时或 72-小时通行证用户；会员 = 年度或月度会员）
   * 邮政编码（如果用户是会员）
   * 性别（由会员自行报告）

## <a name="create-a-new-project"></a>创建新项目
1. 从开始菜单或启动程序启动 **Azure Machine Learning Workbench**。

2. 创建一个新的 Azure 机器学习项目。  单击“项目”页面上的 **+** 按钮，或者单击“文件” > “新建”。
   - 使用“空白项目”模板。
   - 将项目命名为“BikeShare”。 

## <a id="newdatasource"></a>创建新数据源

1. 创建一个新数据源。 单击左侧工具栏上的“数据”按钮（圆柱图标）。 这将显示**数据视图**。

   ![图像：数据视图选项卡](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. 添加一个数据源。 选择 **+** 图标，然后选择“添加数据源”。

   ![图像：“添加数据源”菜单项](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>添加天气数据

1. **数据存储**：选择包含数据的数据存储。 因为你将使用文件，所以请选择“文件/目录”。 选择“下一步”继续。

   ![图像：“文件/目录”菜单项](media/tutorial-bikeshare-dataprep/datasources.png)

2. **文件选择**：添加天气数据。 浏览并选择之前下载的 `BostonWeather.csv` 文件。 单击“下一步”。

   ![图像：选择了 BostonWeater.csv 的“文件选择”](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **文件详细信息**：验证检测到的文件架构。 Azure Machine Learning Workbench 将分析文件中的数据并推断要使用的架构。

   ![图像：文件详细信息](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > 在某些情况下，Workbench 可能检测不到正确的方案。 你应当始终验证参数是否适合你的数据集。 对于天气数据，请验证它们是否已设置为下列值：
   >
   > * __文件类型__：分隔的文件（csv、tsv、txt，等等）
   > * __分隔符__：逗号 [,]
   > * __注释行字符__：未设置值。
   > * __跳过行模式__：不跳过
   > * __文件编码__：utf-8
   > * __提升标题模式__：使用来自第一个文件的标题

   数据预览应当显示以下列：
   * **路径**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   若要继续操作，请选择“下一步”。

4. **数据类型**：查看自动检测到的数据类型。 Azure Machine Learning Workbench 将分析文件中的数据并推断要使用的数据类型。

   对于此数据，请将所有列的 `DATA TYPE` 更改为 `String`。

   > [!NOTE]
   > 在本教程的下文中，`String` 用来突出显示 Workbench 的功能。 

   ![图像：数据类型](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   若要继续操作，请选择“下一步”。 

5. **采样**：若要创建采样方案，请选择“+ 新建”按钮。 选择所添加的新的“前 10000 位”行，然后选择“编辑”。 将“采样策略”设置为“完整文件”，然后选择“应用”。

   ![图像：添加新的采样策略](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   若要使用“完整文件”策略，请选择“完整文件”菜单项，然后选择“设置为活动”。 一个星号显示在“完整文件”旁边以指示它是活动策略。

   ![图像：“完整文件”是活动策略](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   若要继续操作，请选择“下一步”。

6. **路径列**：“路径列”部分允许将完整文件路径包括为导入的数据中的一个列。 选择“不包括路径列”。

   > [!TIP]
   > 如果要导入一个文件夹且其中包含具有不同文件名的许多文件，则将路径包括为列非常有用。 如果文件名中包含你以后希望提取的信息，则这样做也非常有用。

   ![图像：设置为不包括的路径列](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **完成**：若要完成数据源创建，请选择“完成”按钮。

    此时将打开一个名为“BostonWeather”的新数据源选项卡。 网格视图中会显示数据的样本。 样本基于之前指定的活动采样方案。

    请注意，屏幕右侧中的“步骤”窗格会显示创建此数据源时采取的各项操作。

   ![图像：显示了数据源、样本和步骤](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>查看数据源指标

选择位于选项卡的网格视图左上角的“指标”按钮。 此视图显示已采样数据的分布和其他聚合统计信息。

![图像：指标显示](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 若要配置统计信息的可见性，请使用“选择指标”下拉列表。 可以在此处选中和取消选中指标来更改网格视图。

若要返回__数据视图__，请从页面左上角选择“数据”。

## <a name="add-data-source-to-data-preparation-package"></a>向数据准备包添加数据源

1. 选择“准备”以开始准备数据。 

2. 出现提示时，为数据准备包输入一个名称，例如 `BikeShare Data Prep`。 

3. 选择“确定”以继续。

   ![图像：“准备”对话框](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. 一个名为“BikeShare Data Prep”的新包将出现在“数据”选项卡的“数据准备”部分下。 

   若要显示该包，请选择此条目。 

5. 选择 **>>** 按钮进行展开以显示包中包含的__数据流__。 在本例中，“BostonWeather”是唯一的数据流。

   > [!IMPORTANT]
   > 一个包可以包含多个数据流。

   ![图像：包中包含的数据流](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>按值筛选数据
1. 若要筛选数据，请右键单击具有特定值的单元格并选择”筛选器”，然后选择筛选器类型。

2. 对于本教程，请选择包含值 `FM-15` 的一个单元格，然后将筛选器设置为“等于”筛选器。  现在，已对数据进行筛选，仅返回“REPORTTYPE”为 `FM-15` 的行。

   ![图像：筛选器对话框](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 是一种气象站航空例行天气报告 (METAR) 类型。 从经验来看，FM-15 报告是最完整的，缺少的数据很少。

## <a name="remove-a-column"></a>删除列

你不再需要“REPORTTYPE”列。 右键单击列标题并选择“删除列”。

   ![图像：“删除列”选项](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>更改数据类型和删除错误
1. 在选择列标题时按住 __Ctrl__（在 Mac 上为 ⌘ 命令）可以同时选择多个列。 使用此方法选择以下列标题：
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **右键单击**选定的列标题之一，并选择“将字段类型转换为数字”。 这会将列的数据类型转换为数字。

   ![将多个列转换为数字](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. 筛选出错误值。 某些列有数据类型转换问题。 此问题在列的__数据质量栏__中以红色指示。

   若要删除有错误的行，请右键单击“HOURLYDRYBULBTEMPF”列标题。 选择“筛选列”。 将默认的“我希望”用作“保留行”。 更改“条件”下拉列表以选择“不是错误”。 选择“确定”以应用筛选器。

4. 若要消除其他列中的剩余错误行，请针对“HOURLYRelativeHumidity”和“HOURLYWindSpeed”列重复此筛选过程。

## <a name="use-by-example-transformations"></a>使用按示例转换

若要将数据用于针对两小时时间块的预测中，必须针对两小时期间计算平均天气条件。 若要执行此工作，可以使用以下操作：

* 将“DATE”列拆分为单独的“Date”列和“Time”列。 有关详细步骤，请参阅下一部分。

* 基于“Time”列派生一个“Hour_Range”列。 有关详细步骤，请参阅下一部分。

* 基于“DATE”列和“Hour_Range”列派生一个“Date\_Hour\_Range”列。 有关详细步骤，请参阅下一部分。

### <a name="split-column-by-example"></a>按示例拆分列

1. 将“DATE”列拆分为单独的“Date”列和“Time”列。 右键单击“DATE”列标题并选择“按示例拆分列”。

   ![图像：“按示例拆分列”菜单项](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench 将自动识别有意义的分隔符并通过将数据拆分为日期值和时间值来创建两个列。 

3. 选择“确定”以接受拆分操作结果。

   ![图像：拆分列 DATE_1 和 DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>按示例派生列

1. 若要派生一个两小时范围，请右键单击“DATE\_2”列标题并选择“按示例派生列”。

   ![图像：“按示例派生列”菜单项](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   将添加一个具有 null 值的新的空列。

2. 单击新列中的第一个空单元格。 通过在新列中键入 `12AM-2AM` 并按 Enter 提供所需时间范围的示例。

   ![图像：具有 12AM-2AM 值的新列](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench 将根据你提供的示例合成一个计划并对剩余的行应用同一计划。 将根据你提供的示例自动填充所有其他行。 Workbench 还会对数据进行分析并尝试识别边缘情况。 
  
3. 网格上方会显示“正在分析数据”文本，指示 Workbench 正在尝试检测边缘情况。 完成后，状态会更改为“审阅下一个建议行”或“无建议”。 在本例中，将返回“审阅下一个建议行”。

4. 要审阅建议的更改，请选择“审阅下一个建议行”。 应当审阅和更正（如果需要）的单元格突出显示在显示屏上。

   ![图像：审阅行](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    选择“确定”以接受转换。
 
5. 将会返回到“BostonWeather” 的数据的网格视图。 网格现在包含之前添加的三个列。

   ![图像：包含已添加行的网格视图](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  已做的所有更改都将保留在“步骤”窗格上。 转到在“步骤”窗格中创建的步骤，单击向下箭头并选择“编辑”。 此时将显示“按示例派生列”的高级窗口。 所有示例都保留在那里。 还可以通过双击下面的网格中的行来手动添加示例。 选择“取消”可返回到主网格而不应用更改。 还可以通过在执行“按示例派生列”转换时选择“高级模式”来访问此视图。

6. 若要重命名列，请双击列标题并键入“Hour Range”。 按 **Enter** 以保存更改。

   ![重命名列](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. 若要派生日期和小时范围，请以多选方式同时选择“Date\_1”和“Hour Range”列，单击右键，然后选择“按示例派生列”。

   ![按示例派生列](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   针对第一行键入 `Jan 01, 2015 12AM-2AM` 作为示例，然后按 **Enter**。

   Workbench 将根据你提供的示例来确定转换。 在本例中，结果是日期格式已更改并且联接了两小时时间窗口。

   ![图像：示例“Jan 01, 2015 12AM-2AM”](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. 等待状态从“正在分析数据”更改为“审阅下一个建议行”。 这可能要花费几秒钟时间。 选择状态链接以导航到建议行。 

   ![图像：要审阅的建议行](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   该行有一个 null 值，因为源日期值的格式可能是 dd/mm/yyyy 或 mm/dd/yyyy。 键入正确值 `Jan 13, 2015 2AM-4AM` 并按 **Enter**。 Workbench 将使用两个示例来改进其余行的派生。

   ![图像：格式正确的数据](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. 选择“确定”以接受转换。

   ![图像：已完成的转换网格](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > 对于此步骤，可以通过单击“步骤”窗格中的向下箭头来使用“按示例派生列”的高级模式。 在数据网格中，列名称“DATE\_1”和“Hour Range”旁边有复选框。 取消选中“Hour Range”列旁边的复选框来查看这将如何更改输出。 如果缺少“Hour Range”列作为输入，则 **12AM-2AM** 会被视为一个常数并且将附加到派生值。 选择“取消”可返回到主网格而不应用更改。

10. 若要重命名列，请双击标题。 将名称更改为“Date Hour Range”，然后按 **Enter**。

11. 以多选方式同时选择“DATE”、“DATE\_1”、“DATE\_2”和“Hour Range”列。 单击右键，然后选择“删除列”。

## <a name="summarize-data-mean"></a>汇总数据（平均值）

下一个步骤是通过求这些值的平均值来对天气条件进行汇总（按小时范围分组）。

1. 选择“Date Hour Range”列，然后从“转换”菜单中选择“汇总”。

    ![“转换”菜单](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. 若要汇总数据，请将列从页面底部的网格拖到顶部的左窗格和右窗格中。 左窗格包含文本“将列拖到此处以对数据进行分组”。 右窗格包含文本“将列拖到此处以对数据进行汇总”。 

    将“Date Hour Range”列从底部的网格拖到左窗格中。 将“HOURLYDRYBULBTEMPF”、“HOURLYRelativeHumidity”和“HOURLYWindSpeed”拖到右窗格中。 

    在右窗格中，选择“平均值”作为每个列的“聚合”方式。 单击“确定”以完成汇总。

   ![图像：“汇总的数据”屏幕](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>使用脚本转换数据流

将数字列中的数据更改为范围 0-1 以允许某些模型快速聚合。 当前没有内置转换可用来以一般方式执行此转换，但可以使用 Python 脚本来执行此操作。

1. 从“转换”菜单中选择“转换数据流”。

2. 在出现的文本框中输入以下代码。 如果使用的是列名称，则代码应该不需要修改即可工作。 以 Python 编写一个简单的 min-max 规范化逻辑。

    > [!WARNING]
    > 该脚本需要本教程中前面使用的列名称。 如果具有不同的列名称，必须更改脚本中的名称。

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > 该 Python 脚本在结束时必须返回 `df`。 此值用来填充网格。
    
    ![“转换数据流”脚本对话框](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. 选择“确定”以使用脚本。 网格中的数字列现在包含 0-1 范围中的值。

    ![包含介于 0 到 1 之间的值的网格](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

你已完成了对天气数据的准备。 接下来，请准备行程数据。

## <a name="load-trip-data"></a>加载行程数据

1. 若要导入 `201701-hubway-tripdata.csv` 文件，请使用[创建新数据源](#newdatasource)部分中的步骤。 在导入过程中请使用以下选项：

    * __采样方案__：“完整文件”采样方案，使样本成为活动的，并且 
    * __数据类型__：接受默认值。

2. 在导入数据后，选择“准备”按钮以开始准备数据。 选择现有的“BikeShare Data Prep.dprep”包，然后选择“确定”。

    此过程向现有的**数据准备**文件添加一个**数据流**而不创建新文件。

    ![图像：选择现有包](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. 在加载网格后，展开“数据流”。 有两个数据流：“BostonWeather”和“201701-hubway-tripdata”。 选择“201701-hubway-tripdata”条目。

    ![图像：201701-hubway-tripdata 条目](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>使用地图检查器

对于数据准备，针对字符串、数字和地理数据提供了许多称为“检查器”的有用可视化效果，这些指标有助于更好地了解数据和识别出界者。 通过以下步骤使用地图检查器：

1. 以多选方式同时选择“start station latitude”和“start station longitude”列。 右键单击其中一个列并选择“地图”。

    > [!TIP]
    > 若要启用多选，请按住 __Ctrl__（在 Mac 上为 ⌘ 命令）并选择每个列的标题。

    ![图像：地图可视化](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. 若要最大化地图可视化，请选择“最大化”图标。 若要使地图适应窗口，请选择可视化左上方的 **E** 图标。

    ![图像：最大化的](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. 单击“最小化”按钮可返回到网格视图。

## <a name="use-column-statistics-inspector"></a>使用“列统计信息”检查器

若要使用“列统计信息”检查器，请右键单击“tripduration”列并选择“列统计信息”。

![“列统计信息”菜单项](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

此过程在“检查器”窗格中添加一个标题为“tripduration Statistics”的新可视化。

![图像：“tripduration Statistics”检查器](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 行程持续时间的最大值为“961,814 分钟”，大约为两年。 数据集中好像有一些出界者。

## <a name="use-histogram-inspector"></a>使用直方图检查器

若要尝试识别出界者，请右键单击“tripduration”列并选择“直方图”。

![直方图检查器](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

直方图不是很有用，因为出界者使图形变得扭曲。

## <a name="add-column-using-script"></a>使用脚本添加列

1. 右键单击“tripduration”列并选择“添加列(脚本)”。

    ![图像：“添加列(脚本)”菜单](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. 在“添加列(脚本)”对话框中，使用以下值：

    * __新列名__：logtripduration
    * __将此新列插入到以下对象后__：tripduration
    * __新列代码__：`math.log(row.tripduration)`
    * __代码块类型__：表达式

   ![“添加列(脚本)”对话框](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. 选择“确定”以添加“logtripduration”列。

4. 右键单击该列并选择“直方图”。

    ![logtripduration 列的直方图](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  从视觉效果上来看，此直方图像是一个带有异常尾部的正态分布。

## <a name="use-advanced-filter"></a>使用高级筛选器

对数据使用筛选器会以新的分布更新检查器。 右键单击“logtripduration”列并选择“筛选列”。 在“编辑”对话框中，使用下列值：

* __筛选此数字列__：logtripduration
* __我希望__：保留行
* __何时__：下面的任一条件为真（逻辑或）
* __如果此列__：小于
* __值__：9

![筛选器选项](media/tutorial-bikeshare-dataprep/loftripfilter.png)

选择“确定”以应用筛选器。

![应用了筛选器后更新的直方图](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>光圈效果

1. 最大化 **logtripduration** 直方图。 灰色直方图上覆盖了一个蓝色直方图。 此效果称为“光圈效果”：

    * **灰色直方图**表示在执行操作（在本例中为筛选操作）之前的分布。
    * **蓝色直方图**表示在执行操作之后的分布。 

   光圈效果有助于以可视化方式显示对数据的操作的效果。

   ![图像：光圈效果](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 请注意，与以前的直方图相比，蓝色直方图显得较短。 这是因为对新范围中的数据进行了自动重新分段。

2. 若要删除光圈，请选择“编辑”并取消选中“显示光圈”。

    ![直方图的选项](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. 选择“确定”以禁用光圈效果，然后最小化直方图。

### <a name="remove-columns"></a>删除列

在行程数据中，每个行表示一个自行车骑乘事件。 对于本教程，只需使用“开始时间”和“起始站 ID”列。 通过以下方法删除其他列：以多选方式同时选择这两个列，右键单击列标题并选择“保留列”。 其他列将被删除。

![图像：“保留列”选项](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>汇总数据(计数)

若要汇总 2-小时期间的自行车需求，请使用派生列。

1. 右键单击“starttime”列并选择“按示例派生列”。

    ![图像：“按示例派生”选项](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. 例如，为第一行输入值 `Jan 01, 2017 12AM-2AM`。

    > [!IMPORTANT]
    > 在派生列的上一个示例中，我们使用了多个步骤来派生包含日期和时间期间的列。 在此示例中，你会发现，通过提供最终输出的一个示例，可以通过单个步骤执行此操作。

    > [!NOTE]
    > 可以针对任意行提供示例。 对于此示例，`Jan 01, 2017 12AM-2AM` 值对第一行数据有效。

    ![图像：示例数据](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. 一直等到应用程序针对所有行完成值计算。 这可能要花费几秒钟时间。 在分析完成后，使用“审阅下一个建议行”链接来审阅数据。

   ![图像：带有审阅链接的已完成分析](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    确保计算的值正确。 如果不正确，请使用预期的值更新值并按 Enter。 然后，等待分析完成。 完成“审阅下一个建议行”过程，直至看到“无建议”。 在看到“无建议”后，应用程序已查看了边缘情况并对合成的计划感到满意。 在接受转换之前，最好对转换后的数据执行可视化检查。 

4. 选择“确定”以接受转换。 将新创建的列重命名为“Date Hour Range”。

    ![图像：重命名后的列](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. 右键单击“starttime”列标题并选择“删除列”。

6. 若要汇总数据，请从“转换”菜单中选择“汇总”。 若要创建转换，请执行以下操作：

    * 将“Date Hour Range”和“start station id”拖到左（分组依据）窗格中。
    * 将“start station id”拖到右（汇总数据）窗格中。

   ![图像：汇总选项](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. 选择“确定”以接受汇总结果。

## <a name="join-dataflows"></a>联接数据流

若要将天气数据与行程数据进行联接，请使用以下步骤：

1. 从“转换”菜单中选择“联接”。

2. __表__：选择“BostonWeather”作为左侧数据流，选择“201701-hubway-tripdata”作为右侧数据流。 若要继续操作，请选择“下一步”。

    ![图像：表选择](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __键列__：选择两个表中的“Date Hour Range”列，然后选择“下一步”。

    ![图像：按键列进行联接](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __联接类型__：选择“匹配行”作为联接类型，然后选择“完成”。

    ![“匹配行”联接类型](media/tutorial-bikeshare-dataprep/joinscreen.png)

    此过程创建一个名为“Join Result”的新数据流。

## <a name="create-additional-features"></a>创建其他功能

1. 若要创建一个包含周几的列，请右键单击“Date Hour Range”列并选择“按示例派生列”。 为属于周日的日期使用值 __Sun__。 例如 **Jan 01, 2017 12AM-2AM**。 按 **Enter**，然后选择“确定”。 将此列重命名为“Weekday”。

    ![图像：创建包含周几的新列](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. 若要为行创建包含时间期间的列，请右键单击“Date Hour Range”列并选择“按示例派生列”。 为包含“Jan 01, 2017 12AM-2AM”的行使用值“12AM-2AM”。 按 **Enter**，然后选择“确定”。 将此列重命名为“Period”。

    ![图像：period 列](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. 若要删除“Date Hour Range”和“rDate Hour Range”列，请按住“Ctrl”（在 Mac 上为 ⌘ 命令）并选择每个列标题。 单击右键并选择“删除列”。

## <a name="read-data-from-python"></a>通过 Python 读取数据

可以通过 Python 或 PySpark 运行数据准备包并以“数据帧”的形式检索结果。

若要生成示例 Python 脚本，请右键单击“BikeShare Data Prep”并选择“生成数据访问代码文件”。 示例 Python 文件将创建在**项目文件夹**中，并且还会加载到 Workbench 中的一个选项卡中。 下面的 Python 脚本是生成的代码的示例：

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

对于本教程，文件名称为 `BikeShare Data Prep.py`。 本教程后面将使用此文件。

## <a name="save-test-data-as-a-csv-file"></a>将测试数据保存为 CSV 文件

若要将“Join Result”数据流保存为 .CSV 文件，必须更改 `BikeShare Data Prep.py` 脚本。 使用以下代码更新 Python 脚本：

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

从屏幕顶部选择“运行”。 此脚本将提交为本地计算机上的一个“作业”。 在作业状态更改为“已完成”后，文件已写入到指定的位置。

## <a name="substitute-data-sources"></a>替换数据源

在前面的步骤中，你使用了 `201701-hubway-tripdata.csv` 和 `BostonWeather.csv` 数据源来准备测试数据。 若要使用包含其他行程数据文件的包，请使用以下步骤：

1. 使用前面所述的步骤创建一个新**数据源**并对过程进行以下更改：

    * __文件选择__：在选择文件时，以多选方式同时选择剩余的六个行程 tripdata .CSV 文件。

        ![加载剩余的六个文件](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > __+5__ 条目表示除了列出的文件之外，还有五个文件。

    * __文件详细信息__：将“提升标题模式”设置为“所有文件具有相同的标题”。 此值指示每个文件包含相同的标题。

        ![文件详细信息选择](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   保存此数据源的名称，因为后面的步骤将使用此数据源。

2. 选择文件夹图标来查看项目中的文件。 展开“aml\_config”目录，然后选择 `local.runconfig` 文件。

    ![图像 ：local.runconfig 的位置](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. 将下列行添加到 `local.runconfig` 文件的末尾，然后选择磁盘图标来保存文件。

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    此更改将使用包含六个行程数据文件的数据源替换原始数据源。

## <a name="save-training-data-as-a-csv-file"></a>将训练数据保存为 CSV 文件

导航到之前编辑的 Python 文件 `BikeShare Data Prep.py`，并提供一个不同的文件路径来保存训练数据。

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

若要提交新作业，请使用页面顶部的“运行”图标。 将使用新配置提交**作业**。 此作业的输出是训练数据。 此数据是使用你之前创建的相同数据准备步骤创建的。 可能需要花费几分钟时间才能完成作业。

## <a name="next-steps"></a>后续步骤
你已经完成了共享自行车数据准备教程。 在本教程中，你使用 Azure 机器学习服务（预览版）了解了如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure 机器学习数据准备工具以交互方式准备数据
> * 导入、转换和创建测试数据集
> * 生成数据准备包
> * 使用 Python 运行数据准备包
> * 通过将数据准备包重复用于更多输入文件来生成训练数据集

接下来，可以了解有关数据准备的更多信息：
> [!div class="nextstepaction"]
> [数据准备用户指南](data-prep-user-guide.md)
