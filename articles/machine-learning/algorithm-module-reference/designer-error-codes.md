---
title: 排查模块错误
titleSuffix: Azure Machine Learning
description: 使用错误代码排查 Azure 机器学习设计器中的模块异常
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: bfb70aaa092cc62fbff87e9e3e327ee7364f8701
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833807"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>设计器的异常和错误代码（预览版）

本文介绍 Azure 机器学习设计器（预览版）中的错误消息和异常代码，以帮助你排查机器学习管道问题。

可以按照以下步骤在设计器中找到错误消息：  

- 选择失败的模块，转到“输出+日志”选项卡，可以在 azureml-logs 类别下的 70_driver_log.txt 文件中找到详细的日志。

- 对于详细的模块错误，可以在 module_statistics 类别下的 error_info.json 中进行检查。

以下是设计器中模块的错误代码。

## <a name="error-0001"></a>错误 0001  
 如果找不到数据集的一个或多个指定列，则会发生异常。  

 如果为模块选择了列，但所选列在输入数据集中不存在，则会收到此错误。 运行管道时，如果手动键入列名称，或者列选择器提供的建议列在数据集中不存在，则可能发生此错误。  

**解决方法：** 重新访问引发此异常的模块，并验证一个或多个列名称是否正确，以及所有引用的列是否都存在。  

|异常消息|
|------------------------|
|找不到一个或多个指定的列。|
|找不到名称或索引为“{column_id}”的列。|
|名称或索引为“{column_id}”的列在“{arg_name_missing_column}”中不存在。|
|名称或索引为“{column_id}”的列在“{arg_name_missing_column}”中不存在，但在“{arg_name_has_column}”中存在。|
|找不到名称或索引为“{column_names}”的列。|
|名称或索引为“{column_names}”的列在“{arg_name_missing_column}”中不存在。|
|名称或索引为“{column_names}”的列在“{arg_name_missing_column}”中不存在，但在“{arg_name_has_column}”中存在。|


## <a name="error-0002"></a>错误 0002  
 如果无法分析一个或多个参数或将其从指定类型转换为目标方法类型所需的类型，则会发生异常。  

 如果将参数指定为输入，但值类型与预期的类型不同，并且无法执行隐式转换，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 检查模块要求并确定需要哪种值类型（string、integer、double 等）  

|异常消息|
|------------------------|
|无法分析参数。|
|无法分析“{arg_name_or_column}”参数。|
|无法将“{arg_name_or_column}”参数转换为“{to_type}”。|
|无法将“{arg_name_or_column}”参数从“{from_type}”转换为“{to_type}”。|
|无法将“{arg_name_or_column}”参数值“{arg_value}”从“{from_type}”转换为“{to_type}”。|
|无法使用提供的格式“{fmt}”将列“{arg_name_or_column}”中的值“{arg_value}”从“{from_type}”转换为“{to_type}”。|


## <a name="error-0003"></a>错误 0003  
 如果一个或多个输入为 null 或为空，则会发生异常。  

 如果模块的任何输入或参数为 null 或为空，则将在 Azure 机器学习中收到此错误。  例如，如果没有为参数键入任何值，则可能发生此错误。 如果选择包含缺失值的数据集或空数据集，也可能发生此错误。  

**解决方法：**

+ 打开生成该异常的模块，并验证是否已指定所有输入。 确保指定了所有必需的输入。 
+ 确保可以访问从 Azure 存储加载的数据，并且未更改帐户名或密钥。  
+ 检查输入数据中是否有缺失值或 null 值。
+ 如果对数据源使用查询，请验证数据是否以你期望的格式返回。 
+ 检查拼写错误或数据规范中的其他更改。
  
|异常消息|
|------------------------|
|一个或多个输入为 null 或为空。|
|输入“{name}”为 null 或为空。|


## <a name="error-0004"></a>错误 0004  
 如果参数小于或等于特定值，则会发生异常。  

 如果消息中的参数小于模块处理数据所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发该异常的模块，并将参数修改为大于指定值。  

|异常消息|
|------------------------|
|参数应大于边界值。|
|参数“{arg_name}”的值应大于 {lower_boundary}。|
|参数“{arg_name}”的值“{actual_value}”应大于 {lower_boundary}。|


## <a name="error-0005"></a>错误 0005  
 如果参数小于特定值，则会发生异常。  

 如果消息中的参数小于或等于模块处理数据所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发该异常的模块，并将参数修改为大于或等于指定值。  

|异常消息|
|------------------------|
|参数应大于或等于边界值。|
|参数“{arg_name}”的值应大于或等于 {lower_boundary}。|
|参数“{arg_name}”的值“{value}”应大于或等于 {lower_boundary}。|


## <a name="error-0006"></a>错误 0006  
 如果参数大于或等于指定值，则会发生异常。  

 如果消息中的参数大于或等于模块处理数据所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发该异常的模块，并将参数修改为小于指定值。  

|异常消息|
|------------------------|
|参数不匹配。 一个参数应小于另一个参数。|
|参数“{arg_name}”的值应小于参数“{upper_boundary_parameter_name}”的值。|
|参数“{arg_name}”的值“{value}”应小于 {upper_boundary_parameter_name}。|


## <a name="error-0007"></a>错误 0007  
 如果参数大于特定值，则会发生异常。  

 如果在模块属性中指定的值大于允许的值，则将在 Azure 机器学习中收到此错误。 例如，指定的数据可能不在支持的日期范围内，或者当只有三列可用时，可能指示使用五列。 

 如果指定的两组数据需要以某种方式进行匹配，也可能会看到此错误。 例如，如果正在对列重命名，并按索引指定列，则提供的名称数必须与列索引数匹配。 另一个示例可能是使用两列的数学运算，这两列必须具有相同的行数。 

**解决方法：**

 + 打开有问题的模块并查看所有数值属性设置。
 + 确保所有参数值都在该属性支持的值范围内。
 + 如果模块采用多个输入，请确保输入大小相同。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 检查数据集或数据源是否已更改。 有时，在列数、列数据类型或数据大小发生更改后，使用旧版数据的值将失败。  

|异常消息|
|------------------------|
|参数不匹配。 一个参数应小于或等于另一个参数。|
|参数“{arg_name}”的值应小于或等于参数“{upper_boundary_parameter_name}”的值。|
|参数“{arg_name}”的值“{actual_value}”应小于或等于 {upper_boundary}。|
|参数“{arg_name}”的值 {actual_value} 应小于或等于参数“{upper_boundary_parameter_name}”的值 {upper_boundary}。|
|参数“{arg_name}”的值 {actual_value} 应小于或等于 {upper_boundary_meaning} 值 {upper_boundary}。|


## <a name="error-0008"></a>错误 0008  
 如果参数不在范围内，则会发生异常。  

 如果消息中的参数超出模块处理数据所需的边界，则将在 Azure 机器学习中收到此错误。  

 例如，如果尝试使用[添加行](add-rows.md)来合并具有不同列数的两个数据集，则会显示此错误。  

**解决方法：** 重新访问引发该异常的模块，并将参数修改为指定范围内的值。  

|异常消息|
|------------------------|
|参数值不在指定范围内。|
|参数“{arg_name}”的值不在范围内。|
|参数“{arg_name}”的值应在 [{lower_boundary}, {upper_boundary}] 范围内。|
|参数“{arg_name}”的值不在范围内。 {reason}|


## <a name="error-0009"></a>错误 0009  
 如果未正确指定 Azure 存储帐户名称或容器名称，则会发生异常。  

如果为 Azure 存储帐户指定了参数，但无法解析名称或密码，则会在 Azure 机器学习设计器中出现此错误。 密码或帐户名出错可能有多种原因：

 + 帐户类型错误。 机器学习设计器不支持某些新帐户类型。 有关详细信息，请参阅[导入数据](import-data.md)。
 + 输入了错误的帐户名
 + 帐户已不存在
 + 存储帐户的密码不正确或已更改
 + 未指定容器名称，或者容器不存在
 + 未完整指定文件路径（blob 的路径）
   

**解决方法：**

尝试手动输入帐户名、密码或容器路径时，经常会出现此类问题。 建议对[导入数据](import-data.md)模块使用新向导，该向导可以帮助你查找和检查名称。

还要检查帐户、容器或 blob 是否已删除。 使用另一个 Azure 存储实用工具来验证是否正确输入了帐户名和密码，以及容器是否存在。 

Azure 机器学习不支持某些较新的帐户类型。 例如，新的“热”或“冷”存储类型不能用于机器学习。 经典存储帐户和创建为“常规用途”的存储帐户都可以正常使用。

如果指定了 blob 的完整路径，请验证该路径是否指定为 container/blobname，以及帐户中是否同时存在容器和 blob。  

 路径不应包含前导斜杠。 例如，/container/blob 不正确，应输入为 container/blob。  


|异常消息|
|------------------------|
|Azure 存储帐户名称或容器名称不正确。|
|Azure 存储帐户名称“{account_name}”或容器名称“{container_name}”不正确；应提供 container/blob 格式的容器名称。|


## <a name="error-0010"></a>错误 0010  
 如果输入数据集的列名称应匹配但不匹配，则会发生异常。  

 如果消息中的列索引在两个输入数据集中具有不同的列名称，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 使用[编辑元数据](edit-metadata.md)或修改原始数据集，使指定列索引具有相同的列名称。  

|异常消息|
|------------------------|
|输入数据集中具有相应索引的列具有不同的名称。|
|输入数据集(分别为 {dataset1} 和 {dataset2})的列 {col_index} (从零开始)的列名称不同。|


## <a name="error-0011"></a>错误 0011  
 如果传递的列集参数不适用于任何数据集列，则会发生异常。  

 如果指定的列选择与给定数据集中的任何列都不匹配，则将在 Azure 机器学习中收到此错误。  

 如果尚未选择列，而模块至少需要一个列才能正常工作，则也可能收到此错误。  

**解决方法：** 修改模块中的列选择，以便将其应用于数据集中的列。  

 如果模块要求选择特定列，例如标签列，请验证是否选择了正确的列。  

 如果选择了不合适的列，请删除它们并重新运行管道。  

|异常消息|
|------------------------|
|指定的列集不适用于任何数据集列。|
|指定的列集“{column_set}”不适用于任何数据集列。|


## <a name="error-0012"></a>错误 0012  
 如果无法使用传递的参数集创建类的实例，则会发生异常。  

**解决方法：** 此错误无法由用户解决，并将在未来版本中弃用。  

|异常消息|
|------------------------|
|模型未训练，请先训练模型。|
|模型 ({arg_name}) 未训练，请使用已训练的模型。|


## <a name="error-0013"></a>错误 0013  
 如果传递到模块的学习器是无效类型，则会出现异常。  

 只要已训练的模型与连接的评分模块不兼容，就会发生此错误。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**解决方法：**

确定训练模块生成的学习器类型，并确定适合该学习器的评分模块。 

如果使用任何专用训练模块对模型进行了训练，请仅将经过训练的模型连接到相应的专用评分模块。 


|模型类型|训练模块| 评分模块|
|----|----|----|
|任何分类器|[训练模型](train-model.md) |[评分模型](score-model.md)|
|任何回归模型|[训练模型](train-model.md) |[评分模型](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|异常消息|
|------------------------|
|传递了无效类型的学习器。|
|学习器“{arg_name}”的类型无效。|
|学习器“{arg_name}”的类型“{learner_type}”无效。|
|传递了无效类型的学习器。 异常消息: {exception_message}|


## <a name="error-0014"></a>错误 0014  
 如果列的唯一值计数超过了允许数量，则会发生异常。  

 当列包含的唯一值太多时，将发生此错误。  例如，如果指定将某个列作为分类数据处理，但该列中的唯一值太多，以至于无法完成处理，则可能会看到此错误。 如果两个输入中唯一值的数量不匹配，也可能会看到此错误。   

**解决方法：**

打开生成该错误的模块，并标识用作输入的列。 对于某些模块，可以右键单击数据集输入，然后选择“可视化”来获取有关各个列的统计信息，包括唯一值的数量及其分布。

对于打算用于分组或分类的列，请采取措施减少列中唯一值的数量。 可以根据列的数据类型，以不同的方式进行缩减。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到适合你情况的解决方法？ 可以提供有关此主题的反馈，包括生成该错误的模块的名称以及列的数据类型和基数。 我们将使用这些信息为常见情况提供更有针对性的故障排除步骤。   

|异常消息|
|------------------------|
|列的唯一值数量超过了允许数量。|
|列“{column_name}”中唯一值的数量超过了允许数量。|
|列“{column_name}”中唯一值的数量超过了 {limitation} 的元组计数。|


## <a name="error-0015"></a>错误 0015  
 如果数据库连接失败，则会发生异常。  

 如果输入不正确的 SQL 帐户名、密码、数据库服务器或数据库名称，或者由于数据库或服务器问题而无法与数据库建立连接，则会收到此错误。  

**解决方法：** 验证是否正确输入了帐户名、密码、数据库服务器和数据库，以及指定的帐户是否具有正确的权限级别。 验证数据库当前是否可访问。  

|异常消息|
|------------------------|
|建立数据库连接时出错。|
|建立数据库连接 {connection_str} 时出错。|


## <a name="error-0016"></a>错误 0016  
 如果传递到模块的输入数据集应具有兼容的列类型但却没有，则会发生异常。  

 如果在两个或更多个数据集中传递的列类型彼此不兼容，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 使用[编辑元数据](edit-metadata.md)或修改原始输入数据集，<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 以确保列类型兼容。  

|异常消息|
|------------------------|
|输入数据集中具有相应索引的列使用了不兼容的类型。|
|训练数据和测试数据之间的列“{first_col_names}”不兼容。|
|列“{first_col_names}”和“{second_col_names}”不兼容。|
|输入数据集(分别为 {first_dataset_names} 和 {second_dataset_names})的列“{first_col_names}”(从零开始)的列元素类型不兼容。|


## <a name="error-0017"></a>错误 0017  
 如果所选列使用当前模块不支持的数据类型，则会发生异常。  

 例如，如果列选择中包含的列具有无法由模块处理的数据类型（例如，用于数学运算的字符串列，或需要分类特征列的评分列），则可能会在 Azure 机器学习中收到此错误。  

**解决方法：**
 1. 确定出现问题的列。
 2. 查看模块要求。
 3. 修改列以使其符合要求。 可能需要使用以下几个模块进行更改，具体取决于列和尝试进行的转换：
    + 使用[编辑元数据](edit-metadata.md)来更改列的数据类型，或将列用法从特征更改为数值，从分类更改为非分类，等等。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 万不得已时，可能需要修改原始输入数据集。

> [!TIP]
> 找不到适合你情况的解决方法？ 可以提供有关此主题的反馈，包括生成该错误的模块的名称以及列的数据类型和基数。 我们将使用这些信息为常见情况提供更有针对性的故障排除步骤。 

|异常消息|
|------------------------|
|无法处理当前类型的列。 模块不支持该类型。|
|无法处理 {col_type} 类型的列。 模块不支持该类型。|
|无法处理 {col_type} 类型的列“{col_name}”。 模块不支持该类型。|
|无法处理 {col_type} 类型的列“{col_name}”。 模块不支持该类型。 参数名称: {arg_name}。|


## <a name="error-0018"></a>错误 0018  
 如果输入数据集无效，则会发生异常。  

**解决方法：** 在许多情况下，Azure 机器学习中都有可能出现此错误，因此没有单一的解决方法。 通常，此错误意味着作为模块输入提供的数据具有错误的列数，或者数据类型不符合模块的要求。 例如：  

-   模块需要标签列，但没有标记为标签的列，或者尚未选择标签列。  
  
-   模块要求数据是分类类型，但你的数据是数值类型。  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   数据的格式不正确。  
  
-   导入的数据包含无效字符、错误值或超出范围的值。  
-   列为空或包含的缺失值太多。  

 若要确定模块要求以及数据是否符合要求，请查看将使用数据集作为输入的模块的帮助主题。  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->。  

|异常消息|
|------------------------|
|数据集无效。|
|{dataset1} 包含无效的数据。|
|{dataset1} 和 {dataset2} 在列上应保持一致。|
|{dataset1} 包含无效的数据，{reason}。|
|{dataset1} 包含 {invalid_data_category}。 {troubleshoot_hint}|
|{dataset1} 无效，{reason}。 {troubleshoot_hint}|


## <a name="error-0019"></a>错误 0019  
 如果列应包含已排序的值但并非如此，则会发生异常。  

 如果指定的列值不按序显示，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 通过手动修改输入数据集对列值进行排序，然后重新运行模块。  

|异常消息|
|------------------------|
|列中的值未排序。|
|列“{col_index}”中的值未排序。|
|数据集“{dataset}”的列“{col_index}”中的值未排序。|
|参数“{arg_name}”中的值未按“{sorting_order}”顺序排序。|


## <a name="error-0020"></a>错误 0020  
 如果传递到模块的某些数据集中的列数太少，则会发生异常。  

 如果没有为模块选择足够多的列，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问该模块，并确保列选择器选择了正确的列数。  

|异常消息|
|------------------------|
|输入数据集中的列数小于允许的最小列数。|
|输入数据集“{arg_name}”中的列数小于允许的最小列数。|
|输入数据集中的列数小于允许的最小列数({required_columns_count} 列)。|
|输入数据集“{arg_name}”中的列数小于允许的最小列数({required_columns_count} 列)。|


## <a name="error-0021"></a>错误 0021  
 如果传递到模块的某些数据集中的行数太少，则会发生异常。  

 如果数据集中的行数不足，无法执行指定的操作，则会在 Azure 机器学习中出现此错误。 例如，如果输入数据集为空，或者尝试执行的操作需要最小行数才有效，则可能会看到此错误。 此类操作可能包括（但不限于）基于统计方法的分组或分类、特定类型的分箱以及基于计数的学习。  

**解决方法：**

 + 打开返回该错误的模块，然后检查输入数据集和模块属性。 
 + 确保输入数据集不为空，并且有足够的数据行来满足模块帮助中所述的要求。  
 + 如果数据是从外部源加载的，请确保数据源可用，并且数据定义中没有会导致导入过程中行数减少的错误或更改。
 + 如果正在对模块上游的数据执行可能会影响数据类型或值数量的操作（例如清理、拆分或联接操作），请检查这些操作的输出以确定返回的行数。  

|异常消息|
|------------------------|
|输入数据集中的行数小于允许的最小行数。|
|输入数据集中的行数小于允许的最小行数({required_rows_count} 行)。|
|输入数据集中的行数小于允许的最小行数({required_rows_count} 行)。 {reason}|
|输入数据集“{arg_name}”中的行数小于允许的最小行数({required_rows_count} 行)。|
|输入数据集“{arg_name}”中的行数为 {actual_rows_count} 行，小于允许的最小行数({required_rows_count} 行)。|
|输入数据集“{arg_name}”中“{row_type}”行的数量为 {actual_rows_count} 行，小于允许的最小行数({required_rows_count} 行)。|


## <a name="error-0022"></a>错误 0022  
 如果在输入数据集中选择的列数不等于预期数量，则会发生异常。  

 当下游模块或操作需要特定数量的列或输入，而你提供的列或输入太少或太多时，Azure 机器学习中可能会出现此错误。 例如：  

-   指定一个标签列或键列，但不小心选择了多个列。  
  
-   正在对列重命名，但提供的名称比列的数量多或少。  
  
-   源或目标中的列数已更改或与模块使用的列数不匹配。  
  
-   为输入提供了逗号分隔的值列表，但值的数量不匹配，或者不支持多个输入。  

**解决方法：** 重新访问该模块并检查列选择，以确保选择了正确的列数。 验证上游模块的输出以及下游操作的要求。  

 如果使用了可以选择多个列（列索引、所有特征、所有数值等）的列选择选项之一，请验证该选择返回的确切列数。  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 确保上游列的数量或类型未更改。  

 如果正在使用建议数据集训练模型，请记住，推荐器需要的列数有限，并且与用户项对或用户项排名相对应。 在训练模型或拆分建议数据集之前，请删除其他列。 有关详细信息，请参阅[拆分数据](split-data.md)。  

|异常消息|
|------------------------|
|在输入数据集中选择的列数不等于预期数量。|
|在输入数据集中选择的列数不等于 {expected_col_count}。|
|列选择模式“{selection_pattern_friendly_name}”提供的在输入数据集中选择的列数不等于 {expected_col_count}。|
|列选择模式“{selection_pattern_friendly_name}”应提供在输入数据集中选择的 {expected_col_count} 列，但实际上提供了 {selected_col_count} 列。|


## <a name="error-0023"></a>错误 0023  

如果输入数据集的目标列不能用于当前训练器模块，则会发生异常。  

如果目标列（在模块参数中选择）的数据类型无效、包含所有缺失值或者不是预期的分类列，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 重新访问模块输入，以检查标签/目标列的内容。 确保它不包含所有缺失值。 如果模块要求目标列是分类列，请确保目标列中有多个非重复值。  

|异常消息|
|------------------------|
|输入数据集包含不支持的目标列。|
|输入数据集包含不支持的目标列“{column_index}”。|
|输入数据集包含 {learner_type} 类型的学习器不支持的目标列“{column_index}”。|


## <a name="error-0024"></a>错误 0024  
如果数据集不包含标签列，则会发生异常。  

 当模块需要标签列，而数据集没有标签列时，将在 Azure 机器学习中出现此错误。 例如，计算已评分的数据集时，通常需要标签列来计算准确性指标。  

当数据集中存在标签列，但未被 Azure 机器学习正确检测到时，也有可能出现此错误。

**解决方法：**

+ 打开生成该错误的模块，确定是否存在标签列。 列的名称或数据类型无关紧要，只要该列包含要预测的单个结果（或因变量）即可。 如果不确定哪一列包含标签，请查找通用名称，例如类或目标。 
+  如果数据集不包含标签列，可能是因为在上游显式或意外删除了标签列。 也可能是因为数据集不是上游评分模块的输出。
+ 若要将列显式标记为标签列，请添加[编辑元数据](edit-metadata.md)模块并连接数据集。 仅选择标签列，然后从“字段”下拉列表中选择“标签”。 
+ 如果选择了错误的列作为标签，则可以从“字段”中选择“清除标签”，以修复列的元数据。 
  
|异常消息|
|------------------------|
|数据集中没有标签列。|
|“{dataset_name}”中没有标签列。|


## <a name="error-0025"></a>错误 0025  
 如果数据集不包含评分列，则会发生异常。  

 如果计算模型的输入不包含有效评分列，则会在 Azure 机器学习中出现此错误。 例如，用户尝试计算某个数据集，但尚未使用正确的已训练模型对其进行评分，或者评分列显式置于上游。 如果两个数据集中的评分列不兼容，也会发生此异常。 例如，你可能正在尝试将线性回归量与二元分类器的准确性进行比较。  

**解决方法：** 重新访问计算模型的输入，并检查它是否包含一个或多个评分列。 如果不包含，则意味着未对数据集进行评分，或者评分列置于上游模块中。  

|异常消息|
|------------------------|
|数据集中没有评分列。|
|“{dataset_name}”中没有评分列。|
|“{Learner_type}”生成的“{dataset_name}”中没有评分列。 请使用正确类型的学习器为数据集评分。|


## <a name="error-0026"></a>错误 0026  
 如果不允许多个列使用同一名称，则会发生异常。  

 如果多个列具有相同的名称，则会在 Azure 机器学习中出现此错误。 在以下情况下，可能会收到此错误：数据集没有标题行，并且自动分配了列名称Col0、Col1 等等。  

**解决方法：** 如果列的名称相同，则在输入数据集和模块之间插入[编辑元数据](edit-metadata.md)模块。 使用[编辑元数据](edit-metadata.md)中的列选择器选择要重命名的列，并在“新列名称”文本框中键入新名称。  

|异常消息|
|------------------------|
|在参数中指定了相同的列名称。 模块不允许使用相同的列名称。|
|不允许在参数“{arg_name_1}”和“{arg_name_2}”中使用相同的列名称。 请指定不同的名称。|


## <a name="error-0027"></a>错误 0027  
 如果两个对象的大小必须相同但却不同，则会发生异常。  

 这是 Azure 机器学习中的常见错误，可能是由许多情况引起的。  

**解决方法：** 没有特定的解决方法。 不过，你可以检查以下情况：  

-   如果正在对列重命名，请确保每个列表（输入列和新名称列表）具有相同数量的项。  
  
-   如果正在联接或串联两个数据集，请确保它们具有相同的架构。  
  
-   如果正在联接具有多列的两个数据集，请确保键列具有相同的数据类型，并选择选项“允许在所选内容中有重复项并保留列顺序”。  

|异常消息|
|------------------------|
|传递的对象大小不一致。|
|“{friendly_name1}”的大小与“{friendly_name2}”的大小不一致。|


## <a name="error-0028"></a>错误 0028  
 如果列集包含重复的列名称，而这是不允许的，则会发生异常。  

 如果列名称重复，即，不是唯一的，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 如果任何列具有相同的名称，请在输入数据集和引发该错误的模块之间添加[编辑元数据](edit-metadata.md)的实例。 使用[编辑元数据](edit-metadata.md)中的列选择器选择要重命名的列，然后在“新列名称”文本框中键入新的列名称。 如果正在对多个列重命名，请确保在“新列名称”中键入的值是唯一的。  

|异常消息|
|------------------------|
|列集包含重复的列名称。|
|名称“{duplicated_name}”是重复的。|
|名称“{duplicated_name}”在“{arg_name}”中是重复的。|
|名称“{duplicated_name}”是重复的。 详细信息: {details}|


## <a name="error-0029"></a>错误 0029  
 如果传递的 URI 无效，则会发生异常。  

 如果传递的 URI 无效，则会在 Azure 机器学习中出现此错误。  如果符合以下任一情况，则会收到此错误：  

-   为 Azure Blob 存储提供的用于读取或写入的公共或 SAS URI 包含错误。  
  
-   SAS 的时间窗口已过期。  
  
-   通过 HTTP 的 Web URL 源表示文件或环回 URI。  
  
-   通过 HTTP 的 Web URL 包含格式不正确的 URL。  
  
-   远程源无法解析该 URL。  

**解决方法：** 重新访问该模块并验证 URI 的格式。 如果数据源是通过 HTTP 的 Web URL，请确保所需的源不是文件或环回 URI (localhost)。  

|异常消息|
|------------------------|
|传递了无效的 URI。|
|URI“{invalid_url}”无效。|


## <a name="error-0030"></a>错误 0030  
 如果无法下载文件，则会发生异常。  

 如果无法下载文件，则会在 Azure 机器学习中发生此异常。 如果尝试三 (3) 次重试后，尝试从 HTTP 源进行读取失败，则会收到此异常。  

**解决方法：** 验证 HTTP 源的 URI 是否正确，以及该站点当前是否可通过 Internet 访问。  

|异常消息|
|------------------------|
|无法下载文件。|
|下载文件 {file_url} 时出错。|


## <a name="error-0031"></a>错误 0031  
 如果列集中的列数小于所需列数，则会发生异常。  

 如果所选列数小于所需列数，则会在 Azure 机器学习中出现此错误。  如果未选择所需的最小列数，则会收到此错误。  

**解决方法：** 使用列选择器将其他列添加到列选择中。  

|异常消息|
|------------------------|
|列集中的列数小于所需列数。|
|至少需要为输入参数“{arg_name}”指定 {required_columns_count} 列。|
|至少需要为输入参数“{arg_name}”指定 {required_columns_count} 列。 指定的实际列数为 {input_columns_count} 列。|


## <a name="error-0032"></a>错误 0032  
 如果参数不是数字，则会发生异常。  

 如果参数为 double 或 NaN，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 将指定参数修改为使用有效值。  

|异常消息|
|------------------------|
|参数不是数字。|
|“{arg_name}”不是数字。|


## <a name="error-0033"></a>错误 0033  
 如果参数无穷大，则会发生异常。  

 如果参数无穷大，则会在 Azure 机器学习中出现此错误。 如果参数为 `double.NegativeInfinity` 或 `double.PositiveInfinity`，则会收到此错误。  

**解决方法：** 将指定参数修改为有效的值。  

|异常消息|
|------------------------|
|参数必须为有限数字。|
|“{arg_name}”不是有限数字。|
|列“{column_name}”包含无限值。|


## <a name="error-0034"></a>错误 0034  
 如果给定的用户项对存在多个分级，则会发生异常。  

 如果一个用户项对具有多个分级值，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 确保该用户项对只有一个分级值。  

|异常消息|
|------------------------|
|数据集中的值存在多个分级。|
|分级预测数据表中的用户 {user} 和项 {item} 存在多个分级。|
|{dataset} 中的用户 {user} 和项 {item} 存在多个分级。|


## <a name="error-0035"></a>错误 0035  
 如果没有为给定的用户或项提供任何特征，则会发生异常。  

 如果正在尝试使用建议模型进行评分，但找不到特征矢量，则会在 Azure 机器学习中出现此错误。  

**解决方法：**

使用项特征或用户特征时，必须满足 Matchbox 推荐器的特定要求。  此错误表示作为输入提供的用户或项缺少特征矢量。 请确保每个用户或项的数据中都有特征矢量。  

 例如，如果你使用诸如用户的年龄、位置或收入之类的特征对建议模型进行了训练，但现在想为在训练期间未见过的新用户创建评分，则必须为新用户提供一些等效的特征集（即年龄、位置和收入值），才能为他们做出适当的预测。 

 如果这些用户没有任何特征，请考虑使用特征工程来生成适当的特征。  例如，如果没有单独的用户年龄或收入值，则可以生成用于一组用户的近似值。 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 解决方法不适用于你的情况？ 欢迎发送有关本文的反馈，并提供有关你的情况的信息，包括模块和列中的行数。 我们将使用此信息在将来提供更详细的故障排除步骤。

|异常消息|
|------------------------|
|没有为所需的用户或项提供任何特征。|
|{required_feature_name} 的特征是必需的，但未提供。|


## <a name="error-0036"></a>错误 0036  
 如果为给定的用户或项提供了多个特征矢量，则会发生异常。  

 如果多次定义某个特征矢量，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 确保不多次定义该特征矢量。  

|异常消息|
|------------------------|
|用户或项存在重复的特征定义。|


## <a name="error-0037"></a>错误 0037  
 如果指定了多个标签列但只允许一个，则会发生异常。  

 如果选择多个列作为新标签列，则会在 Azure 机器学习中出现此错误。 大多数监督式学习算法要求将单个列标记为目标或标签。  

**解决方法：** 确保选择单个列作为新的标签列。  

|异常消息|
|------------------------|
|指定了多个标签列。|
|在“{dataset_name}”中指定了多个标签列。|


## <a name="error-0039"></a>错误 0039  
 如果操作失败，则会发生异常。  

 如果无法完成内部操作，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 此错误是由多种情况引起的，没有特定的补救措施。  
 下表包含此错误的通用消息，后跟具体情况说明。 

 如果没有可用的详细信息，请访问 [Microsoft 问答页以发送反馈](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html)，并提供有关生成该错误的模块和相关情况的信息。

|异常消息|
|------------------------|
|操作失败。|
|完成以下操作时出错:“{failed_operation}”。|
|完成以下操作时出错:“{failed_operation}”。 原因:“{reason}”。|


## <a name="error-0042"></a>错误 0042  
 如果无法将列转换为其他类型，则会发生异常。  

 如果无法将列转换为指定类型，则会在 Azure 机器学习中出现此错误。  如果模块需要特定的数据类型（例如日期时间、文本、浮点数或整数），但无法将现有列转换为所需的类型，则会收到此错误。  

例如，你可能选择一个列并尝试将其转换为用于数学运算的数值数据类型，但如果该列包含无效数据，则会收到此错误。 

如果尝试将包含浮点数或许多唯一值的列用作分类列，也可能会收到此错误。 

**解决方法：**

+ 打开生成该错误的模块的帮助页面，并验证数据类型要求。
+ 查看输入数据集中列的数据类型。
+ 检查源自所谓的无架构数据源的数据。
+ 检查数据集是否有缺失值或特殊字符，它们可能会导致无法转换为所需的数据类型。 
    + 数值数据类型应保持一致：例如，检查整数列中的浮点数。
    + 在数字列中查找文本字符串或 NA 值。 
    + 布尔值可以根据所需的数据类型转换为适当的表示形式。
    + 检查文本列中的非 unicode 字符、制表符或控制字符
    + 日期时间数据应保持一致，以免出现建模错误；但由于其格式众多，清理起来可能会很复杂。 请考虑使用 <!--the [Execute R Script](execute-r-script.md) or -->[执行 Python 脚本](execute-python-script.md)模块执行清理。  
+ 如有必要，请修改输入数据集中的值，以便可以成功转换列。 修改可能包括分箱、截断或舍入操作、消除离群值或插补缺失值。 有关机器学习中一些常见的数据转换方案，请参阅以下文章：
    + [清理缺失数据](clean-missing-data.md)
    + [规范化数据](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 解决方法不明确或不适用于你的情况？ 欢迎发送有关本文的反馈，并提供有关你的情况的信息，包括模块和列的数据类型。 我们将使用此信息在将来提供更详细的故障排除步骤。  

|异常消息|
|------------------------|
|不允许转换。|
|无法将 {type1} 类型的列转换为 {type2} 类型的列。|
|无法将 {type1} 类型的列“{col_name1}”转换为 {type2} 类型的列。|
|无法将 {type1} 类型的列“{col_name1}”转换为 {type2} 类型的列“{col_name2}”。|


## <a name="error-0044"></a>错误 0044  
 如果无法从现有值派生列的元素类型，则会发生异常。  

 如果无法推断数据集中一个或多个列的类型，则会在 Azure 机器学习中出现此错误。 串联两个或更多个具有不同元素类型的数据集时，通常会发生这种情况。 如果 Azure 机器学习无法确定能够表示一个或多个列中的所有值而不丢失信息的通用类型，它将生成此错误。  

**解决方法：** 确保要合并的两个数据集中的给定列的所有值都属于同一类型（数值、布尔、分类、字符串、日期等）或者可以强制转换为同一类型。  

|异常消息|
|------------------------|
|无法派生列的元素类型。|
|无法派生列“{column_name}”的元素类型 - 所有元素都是空引用。|
|无法派生数据集“{dataset_name}”的列“{column_name}”的元素类型 - 所有元素都是空引用。|


## <a name="error-0045"></a>错误 0045  
 如果因源中存在混合的元素类型而无法创建列，则会发生异常。  

 如果要合并的两个数据集的元素类型不同，则会在 Azure 机器学习中生成此错误。  

**解决方法：** 确保要合并的两个数据集中的给定列的所有值都属于同一类型（数值、布尔、分类、字符串、日期等）。  

|异常消息|
|------------------------|
|无法创建具有混合元素类型的列。|
|无法创建 ID 为“{column_id}”的混合元素类型的列:<br />数据[{row_1}, {column_id}]的类型为“{type_1}”。 <br />数据[{row_2}, {column_id}]的类型为“{type_2}”。|
|无法创建 ID 为“{column_id}”的混合元素类型的列:<br />区块 {chunk_id_1} 中的类型为“{type_1}”。 <br />区块 {chunk_id_2} 中的类型为“{type_2}”，区块大小为 {chunk_size}。|


## <a name="error-0046"></a>错误 0046  
 如果无法在指定路径中创建目录，则会发生异常。  

 如果无法在指定路径中创建目录，则会在 Azure 机器学习中出现此错误。 如果 Hive 查询的输出目录路径的任何部分不正确或不可访问，则会收到此错误。  

**解决方法：** 重新访问该模块，并验证目录路径的格式是否正确，以及是否可以使用当前凭据访问该路径。  

|异常消息|
|------------------------|
|请指定有效的输出目录。|
|无法创建目录 {path}。 请指定有效的路径。|


## <a name="error-0047"></a>错误 0047  
 如果传递到模块的某些数据集中的特征列数太少，则会发生异常。  

 如果要训练的输入数据集不包含算法所需的最小列数，则会在 Azure 机器学习中出现此错误。 通常，数据集为空或仅包含训练列。  

**解决方法：** 重新访问输入数据集，确保除标签列外还有一个或多个其他列。  

|异常消息|
|------------------------|
|输入数据集中的特征列数小于允许的最小列数。|
|输入数据集中的特征列数小于允许的最小列数({required_columns_count} 列)。|
|输入数据集“{arg_name}”中的特征列数小于允许的最小列数(required_columns_count} 列)。|


## <a name="error-0048"></a>错误 0048  
 如果无法打开文件，则会发生异常。  

 如果无法打开文件进行读取或写入，则会在 Azure 机器学习中出现此错误。 由于以下原因，你可能会收到此错误：  

-   容器或文件 (blob) 不存在  
  
-   文件或容器的访问级别不允许你访问该文件  
  
-   文件太大，无法读取或格式错误  

**解决方法：** 重新访问该模块以及要读取的文件。  

 验证容器和文件的名称是否正确。  

 使用 Azure 经典门户或 Azure 存储工具来验证是否有权访问该文件。  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|异常消息|
|------------------------|
|无法打开文件。|
|打开文件 {file_name} 时出错。|
|打开文件 {file_name} 时出错。 存储异常消息: {exception}。|


## <a name="error-0049"></a>错误 0049  
 如果无法分析文件，则会发生异常。  

 如果无法分析文件，则会在 Azure 机器学习中出现此错误。 如果在[导入数据](import-data.md)模块中选择的文件格式与文件的实际格式不匹配，或者文件包含无法识别的字符，则会收到此错误。  

**解决方法：** 重新访问该模块，如果文件格式选择与文件格式不匹配，则予以纠正。 如果可能，请检查文件以确认它不包含任何非法字符。  

|异常消息|
|------------------------|
|无法分析文件。|
|分析 {file_format} 文件时出错。|
|分析 {file_format} 文件 {file_name} 时出错。|
|分析 {file_format} 文件时出错。 原因: {failure_reason}。|
|分析 {file_format} 文件 {file_name} 时出错。 原因: {failure_reason}。|


## <a name="error-0052"></a>错误 0052  
 如果未正确指定 Azure 存储帐户密钥，则会发生异常。  

 如果用于访问 Azure 存储帐户的密钥不正确，则会在 Azure 机器学习中出现此错误。 例如，如果 Azure 存储密钥在复制和粘贴时被截断，或者使用了错误的密钥，则可能会看到此错误。  

 有关如何获取 Azure 存储帐户密钥的详细信息，请参阅[查看、复制和重新生成存储访问密钥](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  

**解决方法：** 重新访问该模块，并验证该帐户的 Azure 存储密钥是否正确；必要时，请从 Azure 经典门户重新复制密钥。  

|异常消息|
|------------------------|
|Azure 存储帐户密钥不正确。|


## <a name="error-0053"></a>错误 0053  
 如果 Matchbox 建议没有用户特征或项，则会发生异常。  

 如果找不到特征矢量，则会在 Azure 机器学习中生成此错误。  

**解决方法：** 确保输入数据集中存在特征矢量。  

|异常消息|
|------------------------|
|需要用户特征或/和项，但未提供。|


## <a name="error-0056"></a>错误 0056  
 如果为操作选择的列违反要求，则会发生异常。  

 为操作选择列时，如果该操作需要特定数据类型的列，则会在 Azure 机器学习中出现此错误。 

 如果该列是正确的数据类型，但你正在使用的模块要求该列还必须标记为特征列、标签列或分类列，则也可能出现此错误。  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**解决方法：**

1.  查看当前所选列的数据类型。 

2. 确定所选列是否为分类列、标签列或特征列。  
  
3.  查看在其中进行列选择的模块的帮助主题，以确定是否对数据类型或列用法有特定要求。  
  
3.  在此操作期间，使用[编辑元数据](edit-metadata.md)更改列类型。 如果需要将列用于下游操作，请确保使用[编辑元数据](edit-metadata.md)的另一个实例将列类型改回其原始值。  

|异常消息|
|------------------------|
|一个或多个选定列不在允许的类别中。|
|名为“{col_name}”的列不在允许的类别中。|


## <a name="error-0057"></a>错误 0057  
 如果尝试创建的文件或 blob 已存在，则会发生异常。  

 如果正在使用[导出数据](export-data.md)模块或其他模块将 Azure 机器学习中的管道结果保存到 Azure blob 存储，但尝试创建的文件或 blob 已存在，则会发生此异常。   

**解决方法：**

 除非先前将“Azure blob 存储写入模式”属性设置为”错误”，否则不会收到此错误。 按照设计，如果尝试将数据集写入已存在的 blob，此模块将引发错误。

 - 打开模块属性，将“Azure blob 存储写入模式”属性更改为”覆盖”。
 - 或者，可以键入其他目标 blob 或文件的名称，并确保指定一个尚不存在的 blob。  

|异常消息|
|------------------------|
|文件或 Blob 已存在。|
|文件或 Blob“{file_path}”已存在。|


## <a name="error-0058"></a>错误 0058  
 如果数据集不包含所需的标签列，则会在 Azure 机器学习中出现此错误。  

 当提供的标签列与学习器需要的数据或数据类型不匹配，或者具有错误的值时，也有可能发生此异常。 例如，如果在训练二元分类器时使用实值标签列，则会生成此异常。  

**解决方法：** 解决方法取决于所使用的学习器或训练器，以及数据集中列的数据类型。 首先，验证机器学习算法或训练模块的要求。  

 重新访问输入数据集。 验证你希望被视为标签的列具有的数据类型是否适用于所创建的模型。  

 检查输入中是否有缺失值，并在必要时消除或替换它们。  

 如有必要，请添加[编辑元数据](edit-metadata.md)模块，并确保将标签列标记为标签。  

|异常消息|
|------------------------|
|无法将标签列的值与评分标签列的值进行比较。|
|“{dataset_name}”中的标签列不符合预期。|
|“{dataset_name}”中的标签列不符合预期，{reason}。|
|“{dataset_name}”中不应有标签列“{column_name}”。|
|“{dataset_name}”中不应有标签列“{column_name}”，{reason}。|


## <a name="error-0059"></a>错误 0059  
 如果无法分析列选取器中指定的列索引，则会发生异常。  

 如果无法分析使用列选择器时指定的列索引，则会在 Azure 机器学习中出现此错误。  如果列索引采用无法分析的无效格式，则会收到此错误。  

**解决方法：** 修改列索引以使用有效的索引值。  

|异常消息|
|------------------------|
|无法分析一个或多个指定的列索引或索引范围。|
|无法分析列索引或范围“{column_index_or_range}”。|


## <a name="error-0060"></a>错误 0060  
 如果在列选取器中指定的列范围超出了范围，则会发生异常。  

 如果在列选择器中指定的列范围超出了范围，则会在 Azure 机器学习中出现此错误。 如果列选取器中的列范围与数据集中的列不对应，则会收到此错误。  

**解决方法：** 修改列选取器中的列范围，以对应于数据集中的列。  

|异常消息|
|------------------------|
|指定了无效或超出范围的列索引范围。|
|列范围“{column_range}”无效或超出范围。|


## <a name="error-0061"></a>错误 0061  
 如果尝试向数据表添加的行的列数不同于该表的列数，则会发生异常。  

 如果尝试向数据集添加的行的列数不同于该数据集的列数，则会在 Azure 机器学习中出现此错误。  如果要添加到数据集的行的列数不同于输入数据集的列数，则会收到此错误。  如果列数不同，则不能将该行追加到该数据集。  

**解决方法：** 修改输入数据集，使其列数与所添加的行的列数相同，或修改所添加的行，使其列数与数据集的列数相同。  

|异常消息|
|------------------------|
|所有表必须具有相同的列数。|
|区块“{chunk_id_1}”中的列与区块大小为 {chunk_size} 的区块“{chunk_id_2}”中的列不同。|
|文件“{filename_1}”中的列计数(计数={column_count_1})与文件“{filename_2}”中的列计数(计数={column_count_2})不同。|


## <a name="error-0062"></a>错误 0062  
 尝试比较具有不同学习器类型的两个模型时发生异常。  

 如果无法比较两个不同评分数据集的计算指标，则会在 Azure 机器学习中生成此错误。 在这种情况下，无法比较用于生成这两个评分数据集的模型的有效性。  

**解决方法：** 验证评分结果是否由相同类型的机器学习模型（二元分类、回归、多类分类、建议、聚类分析、异常情况检测等）生成进行比较的所有模型都必须具有相同的学习器类型。  

|异常消息|
|------------------------|
|所有模型都必须具有相同的学习器类型。|
|获取了不兼容的学习器类型:“{actual_learner_type}”。 预期的学习器类型为:“{expected_learner_type_list}”。|


## <a name="error-0064"></a>错误 0064  
 如果未正确指定 Azure 存储帐户名称或存储密钥，则会发生异常。  

 如果未正确指定 Azure 存储帐户名称或存储密钥，则会在 Azure 机器学习中出现此错误。 如果为存储帐户输入的帐户名或密码不正确，则会收到此错误。 如果手动输入帐户名或密码，则可能会发生这种情况。 如果帐户已被删除，也可能会发生这种情况。  

**解决方法：** 验证是否输入了正确的帐户名和密码，以及该帐户是否存在。  

|异常消息|
|------------------------|
|Azure 存储帐户名称或存储密钥不正确。|
|Azure 存储帐户名称“{account_name}”或帐户名称的存储密钥不正确。|


## <a name="error-0065"></a>错误 0065  
 如果未正确指定 Azure blob 名称，则会发生异常。  

 如果未正确指定 Azure blob 名称，则会在 Azure 机器学习中出现此错误。  如果出现以下情况，你将收到此错误：  

-   在指定容器中找不到该 blob。  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   当格式为带编码的 Excel 或 CSV 时，仅将容器指定为[导入数据](import-data.md)请求中的源；这些格式不允许将容器中所有 blob 的内容串联在一起。  
  
-   SAS URI 不包含有效 blob 的名称。  

**解决方法：** 重新访问引发该异常的模块。 验证存储帐户的容器中是否确实存在指定的 blob，以及你是否有权查看该 blob。 如果具有带编码格式的 Excel 或 CSV，请验证输入的格式是否为 containername/filename。 验证 SAS URI 是否包含有效 blob 的名称。  

|异常消息|
|------------------------|
|Azure 存储 blob 名称不正确。|
|Azure 存储 blob 名称“{blob_name}”不正确。|
|前缀为“{blob_name_prefix}”的 Azure 存储 blob 名称不存在。|
|在容器“{container_name}”下找不到任何 Azure 存储 blob。|
|使用通配符路径“{blob_wildcard_path}”找不到任何 Azure 存储 blob。|


## <a name="error-0066"></a>错误 0066  
 如果无法将资源上载到 Azure Blob，则会发生异常。  

 如果无法将资源上载到 Azure Blob，则会在 Azure 机器学习中出现此错误。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 两者都保存在与包含输入文件的帐户相同的 Azure 存储帐户中。  

**解决方法：** 重新访问该模块。 验证 Azure 帐户名称、存储密钥和容器是否正确，以及帐户是否有权写入容器。  

|异常消息|
|------------------------|
|无法将资源上载到 Azure 存储。|
|无法将文件“{source_path}”作为“{dest_path}”上载到 Azure 存储。|


## <a name="error-0067"></a>错误 0067  
 如果数据集的列数不同于预期的列数，则会发生异常。  

 如果数据集的列数不同于预期的列数，则会在 Azure 机器学习中出现此错误。  如果数据集中的列数与模块在执行期间所需的列数不同，则会收到此错误。  

**解决方法：** 修改输入数据集或参数。  

|异常消息|
|------------------------|
|数据表中存在意外的列数。|
|数据集“{dataset_name}”中存在意外的列数。|
|应为“{expected_column_count}”列，但找到了“{actual_column_count}”列。|
|在输入数据集“{dataset_name}”中，应为“{expected_column_count}”列，但找到了“{actual_column_count}”列。|


## <a name="error-0068"></a>错误 0068  
 如果指定的 Hive 脚本不正确，则会发生异常。  

 如果 Hive QL 脚本中存在语法错误，或者 Hive 解释器在执行查询或脚本时遇到错误，则会在 Azure 机器学习中出现此错误。  

**解决方法：**

来自 Hive 的错误消息通常会报告到错误日志中，以便你根据特定错误采取措施。 

+ 打开模块并检查查询是否有错误。  
+ 登录到 Hadoop 群集的 Hive 控制台并运行查询，以验证查询在 Azure 机器学习外部能否正常工作。  
+ 尝试将注释放在 Hive 脚本的单独行中，而不是将可执行语句和注释混合在一行中。  

### <a name="resources"></a>资源

有关用于机器学习的 Hive 查询的帮助信息，请参阅以下文章：

+ [从 Azure Blob 存储创建 Hive 表和加载数据](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [使用 Hive 查询在表中浏览数据](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [使用 Hive 查询在 Hadoop 群集中为数据创建功能](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive for SQL Users Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)（面向 SQL 用户的 Hive 速查表 (PDF)）

  
|异常消息|
|------------------------|
|Hive 脚本不正确。|


## <a name="error-0069"></a>错误 0069  
 如果指定的 SQL 脚本不正确，则会发生异常。  

 如果指定的 SQL 脚本存在语法问题，或者脚本中指定的列或表无效，则会在 Azure 机器学习中出现此错误。 

 如果 SQL 引擎在执行查询或脚本时遇到任何错误，你将收到此错误。 SQL 错误消息通常会报告到错误日志中，以便你根据特定错误采取措施。  

**解决方法：** 重新访问该模块并检查 SQL 查询是否有错误。  

 直接登录到数据库服务器并运行查询，以验证查询在 Azure ML 外部能否正常工作。  

 如果模块异常报告了 SQL 生成的消息，请根据报告的错误采取措施。 例如，错误消息有时会对可能的错误提供特定指导：
+ 没有这样的列或缺少数据库，表示你可能键入了错误的列名。 如果确定列名正确，请尝试使用括号或引号将列标识符括起来。
+ \<SQL keyword\> 附近有 SQL 逻辑错误，表示在指定关键字前面可能有语法错误

  
|异常消息|
|------------------------|
|SQL 脚本不正确。|
|SQL 查询“{sql_query}”不正确。|
|SQL 查询“{sql_query}”不正确。 异常消息: {exception}。|


## <a name="error-0070"></a>错误 0070  
 如果尝试访问的 Azure 表不存在，则会发生异常。  

 如果尝试访问的 Azure 表不存在，则会在 Azure 机器学习中出现此错误。 如果在 Azure 表存储中读取或写入 Azure 存储中指定的表时，该表不存在，则会收到此错误。 如果错误键入所需表的名称，或者目标名称和存储类型不匹配，则可能会发生这种情况。 例如，你打算从表中读取数据，但输入的是 blob 的名称。  

**解决方法：** 重新访问该模块，以验证表的名称是否正确。  

|异常消息|
|------------------------|
|Azure 表不存在。|
|Azure 表“{table_name}”不存在。|


## <a name="error-0072"></a>错误 0072  
 如果连接超时，则会发生异常。  

 如果连接超时，则会在 Azure 机器学习中出现此错误。如果数据源或目标当前存在连接问题，例如 Internet 连接缓慢，或者如果数据集很大，并且/或者要读取数据的 SQL 查询执行复杂的处理，则会收到此错误。  

**解决方法：** 确定当前是否存在与 Azure 存储或 Internet 连接缓慢的问题。  

|异常消息|
|------------------------|
|发生连接超时。|


## <a name="error-0073"></a>错误 0073  
 如果在将列转换为其他类型时出错，则会发生异常。  

 如果无法将列转换为其他类型，则会在 Azure 机器学习中出现此错误。  如果模块需要特定类型，但无法将列转换为新类型，则会收到此错误。  

**解决方法：** 修改输入数据集，以便根据内部异常来转换列。  

|异常消息|
|------------------------|
|无法转换列。|
|无法将列转换为 {target_type}。|


## <a name="error-0075"></a>错误 0075  
如果在量化数据集时使用了无效的分箱函数，则会发生异常。  

如果尝试使用不受支持的方法对数据进行分箱，或者参数组合无效，则会在 Azure 机器学习中出现此错误。  

**解决方法：**

较早版本的 Azure 机器学习引入了对此事件的错误处理，该版本允许对分箱方法进行更多自定义。 目前，所有分箱方法均基于下拉列表中的选择，因此从技术上讲，再也不可能出现此错误。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|异常消息|
|------------------------|
|使用了无效的分箱函数。|


## <a name="error-0077"></a>错误 0077  
 如果传递了未知的 blob 文件写入模式，则会发生异常。  

 如果在 blob 文件目标或源的规范中传递了无效的参数，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 在几乎所有用于在 Azure blob 存储中导入或导出数据的模块中，都是通过下拉列表来分配控制写入模式的参数值；因此，不可能传递无效的值，从而也不应出现此错误。 此错误将在以后的版本中弃用。  

|异常消息|
|------------------------|
|blob 写入模式不受支持。|
|blob 写入模式 {blob_write_mode} 不受支持。|


## <a name="error-0078"></a>错误 0078  
 如果[导入数据](import-data.md)的 HTTP 选项收到指示重定向的 3xx 状态代码，则会发生异常。  

 如果[导入数据](import-data.md)的 HTTP 选项收到指示重定向的 3xx（301、302、304 等）状态代码，则会在 Azure 机器学习中出现此错误。 如果尝试连接到将浏览器重定向到另一个页面的 HTTP 源，则会收到此错误。 出于安全原因，不允许将重定向网站作为 Azure 机器学习的数据源。  

**解决方法：** 如果该网站是受信任的网站，请直接输入重定向 URL。  

|异常消息|
|------------------------|
|不允许 HTTP 重定向。|


## <a name="error-0079"></a>错误 0079  
 如果未正确指定 Azure 存储容器名称，则会发生异常。  

 如果未正确指定 Azure 存储容器名称，则会在 Azure 机器学习中出现此错误。 如果在写入 Azure Blob 存储时未使用“以容器开头的 blob 路径”选项指定容器和 blob（文件）名称，则会收到此错误。  

**解决方法：** 重新访问[导出数据](export-data.md)模块，并确保 blob 的指定路径同时包含容器和文件名，格式为 container/filename。  

|异常消息|
|------------------------|
|Azure 存储容器名称不正确。|
|Azure 存储容器名称“{container_name}”不正确；应提供 container/blob 格式的容器名称。|


## <a name="error-0080"></a>错误 0080  
 如果模块不允许使用包含所有缺失值的列，则会发生异常。  

 如果模块使用的一个或多个列包含所有缺失值，则会在 Azure 机器学习中生成此错误。 例如，如果某个模块正在计算每个列的聚合统计信息，则它无法对不包含任何数据的列进行运算。 在这种情况下，模块执行会因该异常而停止。  

**解决方法：** 重新访问输入数据集，并删除包含所有缺失值的列。  

|异常消息|
|------------------------|
|不允许使用包含所有缺失值的列。|
|列 {col_index_or_name} 包含所有缺失值。|


## <a name="error-0081"></a>错误 0081  
 如果要减少的维数等于输入数据集中的特征列数，而且该数据集中至少包含一个稀疏特征列，则会在 PCA 模块中发生异常。  

 如果满足以下条件，则会在 Azure 机器学习中生成此错误：(a) 输入数据集至少有一个稀疏列；(b) 请求的最终维数与输入维数相同。  

**解决方法：** 考虑减少输出中的维数，使其小于输入中的维数。 这在 PCA 应用程序中很典型。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|异常消息|
|------------------------|
|对于包含稀疏特征列的数据集，要减少到的维数应小于特征列数。|


## <a name="error-0082"></a>错误 0082  
 如果模型不能成功地反序列化，则会发生异常。  

 当较新版本的 Azure 机器学习运行时因重大更改而无法加载已保存的机器学习模型或转换时，将在 Azure 机器学习中出现此错误。  

**解决方法：** 必须重新运行生成该模型或转换的训练管道，并且必须重新保存该模型或转换。  

|异常消息|
|------------------------|
|模型无法反序列化，因为它可能使用较旧的序列化格式进行了序列化。 请重新训练并重新保存该模型。|


## <a name="error-0083"></a>错误 0083  
 如果用于训练的数据集不可用于具体类型的学习器，则会发生异常。  

 如果数据集与正在训练的学习器不兼容，则会在 Azure 机器学习中生成此错误。 例如，数据集的每一行可能至少包含一个缺失值，因此，在训练过程中将跳过整个数据集。 在其他情况下，某些机器学习算法（例如异常情况检测）不希望存在标签，因此如果数据集中有标签，则可能引发此异常。  

**解决方法：** 查阅所用学习器的相关文档，以检查对输入数据集的要求。 检查列，看是否存在所有必需的列。  

|异常消息|
|------------------------|
|用于训练的数据集无效。|
|{data_name} 包含无法用于训练的数据。|
|{data_name} 包含无法用于训练的数据。 学习器类型: {learner_type}。|
|{data_name} 包含无法用于训练的数据。 学习器类型: {learner_type}。 原因: {reason}。|
|无法对训练数据 {data_name} 应用“{action_name}”操作。 原因: {reason}。|


## <a name="error-0084"></a>错误 0084  
 计算从 R 脚本生成的分数时发生异常。 当前不支持该操作。  

 如果尝试使用某个模块来计算具有 R 脚本输出（包含分数）的模型，则会在 Azure 机器学习中出现此错误。  

**解决方法：**

|异常消息|
|------------------------|
|当前不支持计算自定义模型生成的分数。|


## <a name="error-0085"></a>错误 0085  
 如果脚本计算失败且出现错误，则会发生异常。  

 运行包含语法错误的自定义脚本时，将在 Azure 机器学习中出现此错误。  

**解决方法：** 在外部编辑器中查看代码，并检查错误。  

|异常消息|
|------------------------|
|计算脚本期间出错。|
|计算脚本期间发生以下错误，请查看输出日志以了解详细信息:<br />---------- 来自 {script_language} 解释器的错误消息开头 ----------<br />{message}<br />---------- 来自 {script_language} 解释器的错误消息结尾 ----------|


## <a name="error-0090"></a>错误 0090  
 Hive 表创建失败时发生异常。  

 使用[导出数据](export-data.md)或其他选项将数据保存到 HDInsight 群集时，如果无法创建指定的 Hive 表，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 检查与群集关联的 Azure 存储帐户名称，并验证是否在模块属性中使用同一帐户。  

|异常消息|
|------------------------|
|无法创建 Hive 表。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称与通过模块参数传入的名称相同。|
|无法创建 Hive 表“{table_name}”。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称与通过模块参数传入的名称相同。|
|无法创建 Hive 表“{table_name}”。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称为“{cluster_name}”。|


## <a name="error-0102"></a>错误 0102  
 在不能提取 ZIP 文件时引发。  

 如果正在导入扩展名为 .zip 的压缩包，但该包不是 zip 文件，或者该文件使用的 zip 格式不受支持，则会在 Azure 机器学习中出现此错误。  

**解决方法：** 确保所选文件是有效的 .zip 文件，并且使用受支持的压缩算法之一进行了压缩。  

 如果以压缩格式导入数据集时遇到此错误，请验证所有包含的文件是否使用受支持的文件格式之一，并且是否为 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 尝试将所需文件读取到新压缩的压缩文件夹中，然后尝试再次添加自定义模块。  

|异常消息|
|------------------------|
|给定 ZIP 文件的格式不正确。|


## <a name="error-0105"></a>错误 0105  
 当模块定义文件包含不受支持的参数类型时，将显示此错误  
  
 如果创建自定义模块 xml 定义，但定义中的形参或实参类型与支持的类型不匹配，则会在 Azure 机器学习中生成此错误。  
  
**解决方法：** 确保自定义模块 xml 定义文件中任何 Arg 元素的 type 属性都是受支持的类型。  
  
|异常消息|  
|------------------------|  
|参数类型不受支持。|  
|指定了不支持的参数类型“{0}”。|  


## <a name="error-0107"></a>错误 0107  
 当模块定义文件定义了不支持的输出类型时引发  
  
 当自定义模块 xml 定义中输出端口的类型与支持的类型不匹配时，将在 Azure 机器学习中生成此错误。  
  
**解决方法：** 确保自定义模块 xml 定义文件中 Output 元素的 type 属性是受支持的类型。  
  
|异常消息|  
|------------------------|  
|输出类型不受支持。|  
|指定了不支持的输出类型“{output_type}”。|  


## <a name="error-0125"></a>错误 0125  
 在多个数据集的架构不匹配时引发。  

**解决方法：**

|异常消息|
|------------------------|
|数据集架构不匹配。|


## <a name="error-0127"></a>错误 0127  
 图像像素大小超出了允许的限制  

 如果正在从图像数据集中读取图像进行分类，但图像大小超过模型可以处理的大小，则会发生此错误。  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|异常消息|
|------------------------|
|图像像素大小超出了允许的限制。|
|文件“{file_path}”中的图像像素大小超出了允许的限制:“{size_limit}”。|


## <a name="error-0128"></a>错误 0128  
 分类列的条件概率数超出了限制。  

**解决方法：**

|异常消息|
|------------------------|
|分类列的条件概率数超出了限制。|
|分类列的条件概率数超出了限制。 “{column_name_or_index_1}”和“{column_name_or_index_2}”列是有问题的配对。|


## <a name="error-0129"></a>错误 0129  
 数据集中的列数超出了允许的限制。  

**解决方法：**

|异常消息|
|------------------------|
|数据集中的列数超出了允许的限制。|
|数据集“{dataset_name}”中的列数超出了允许的限制。|
|数据集“{dataset_name}”中的列数超出了“{component_name}”允许的限制。|
|数据集“{dataset_name}”中的列数超出了“{component_name}”允许的限制({limit_columns_count} 列)。|


## <a name="error-0134"></a>错误 0134
当标签列缺失或包含的标记行数不足时，会发生异常。  

当模块需要标签列，但你没有在列选择中包含一个标签列，或者标签列缺少太多值时，将出现此错误。

当上一个操作更改数据集，使得下游操作没有足够的行时，也会出现此错误。 例如，假设使用“分区和采样”模块中的表达式按值划分数据集。 如果没有为表达式找到匹配项，则分区产生的数据集之一将为空。

解决方法： 

 如果在列选择中包含标签列，但无法识别该列，请使用[编辑元数据](edit-metadata.md)模块将其标记为标签列。

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  然后，可以使用[清理缺失数据](clean-missing-data.md)模块来删除标签列中包含缺失值的行。 

 检查输入数据集，确保它们包含有效数据并且有足够的行来满足操作要求。 对于许多算法，当它们需要数据的最小行数，但数据仅包含几行或仅包含一个标题时，它们将生成错误消息。

|异常消息|
|------------------------|
|当标签列缺失或包含的标记行数不足时，会发生异常。|
|当标签列缺失或包含的标记行数少于 {required_rows_count} 行时，会发生异常。|
|当数据集 {dataset_name} 中的标签列缺失或包含的标记行数少于 {required_rows_count} 行时，会发生异常。|


## <a name="error-0138"></a>错误 0138  
 内存已耗尽，无法完成模块运行。 减少数据集采样可能有助于缓解该问题。  

 当正在运行的模块所需的内存超过 Azure 容器中的可用内存时，会发生此错误。 如果正在处理大型数据集，但内存无法容纳当前操作，则会发生这种情况。  

**解决方法：** 如果正在尝试读取大型数据集，但无法完成该操作，则减少数据集采样可能会有所帮助。  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|异常消息|
|------------------------|
|内存已耗尽，无法完成模块运行。|
|内存已耗尽，无法完成模块运行。 详细信息: {details}|


## <a name="error-0141"></a>错误 0141  
 如果所选数值列的数量以及分类列和字符串列中唯一值的数量太少，则会发生异常。  

 当所选列中的唯一值不足以执行操作时，将在 Azure 机器学习中出现此错误。  

**解决方法：** 某些操作对特征列和分类列执行统计操作，如果没有足够的值，操作可能会失败或返回无效结果。 检查数据集，以查看特征列和标签列中有多少个值，并确定你尝试执行的操作在统计上是否有效。  

 如果源数据集有效，你还可以检查某些上游数据操作或元数据操作是否更改了数据并删除了某些值。  

 如果上游操作包括拆分、采样或重新采样，请验证输出是否包含预期的行数和值数。  

|异常消息|
|------------------------|
|所选数值列的数量以及分类列和字符串列中唯一值的数量太少。|
|所选数值列以及分类列和字符串列中唯一值的总数(当前为 {actual_num} 个)应至少为 {lower_boundary}。|


## <a name="error-0154"></a>错误 0154  
 当用户尝试对列类型不兼容的键列上的数据进行联接时，会发生异常。

|异常消息|
|------------------------|
|键列元素类型不兼容。|
|键列元素类型不兼容。(左: {keys_left}；右: {keys_right})|


## <a name="error-0155"></a>错误 0155  
 当数据集的列名称不是字符串时，会发生异常。

|异常消息|
|------------------------|
|数据帧列名称必须是字符串类型。 列名称不是字符串。|
|数据帧列名称必须是字符串类型。 列名称 {column_names} 不是字符串。|


## <a name="error-0156"></a>错误 0156  
 从 Azure SQL 数据库读取数据失败时，会发生异常。

|异常消息|
|------------------------|
|从 Azure SQL 数据库读取数据失败。|
|从 Azure SQL 数据库读取数据失败: {detailed_message} DB: {database_server_name}:{database_name} 查询: {sql_statement}|


## <a name="error-0157"></a>错误 0157  
 找不到数据存储。

|异常消息|
|------------------------|
|数据存储信息无效。|
|数据存储信息无效。 未能获取工作区“{workspace_name}”中的 AzureML 数据存储“{datastore_name}”。|


## <a name="error-0158"></a>错误 0158
 转换目录无效时引发。

|异常消息|
|------------------------------------------------------------|
|给定的 TransformationDirectory 无效。|
|TransformationDirectory“{arg_name}”无效。 原因: {reason}。 请重新运行训练实验，以生成转换文件。 如果训练实验被删除，请重新创建并保存转换文件。|
|TransformationDirectory“{arg_name}”无效。 原因: {reason}。 {troubleshoot_hint}|


## <a name="error-0159"></a>错误 0159
 如果传递到模块的模型目录无效，则会发生异常。 

|异常消息|
|------------------------------------------------------------|
|给定的 ModelDirectory 无效。|
|ModelDirectory“{arg_name}”无效。|
|ModelDirectory“{arg_name}”无效。 原因: {reason}。|
|ModelDirectory“{arg_name}”无效。 原因: {reason}。 {troubleshoot_hint}|


## <a name="error-1000"></a>错误 1000  
内部库异常。  

提供此错误是为了捕获未处理的内部引擎错误。 因此，导致此错误的原因可能会因生成此错误的模块而异。  

若要获得更多帮助，建议你将此错误附带的详细消息以及具体情况说明（包括用作输入的数据）发布到 Azure 机器学习论坛。 此反馈将帮助我们确定错误优先级和最重要的问题，以便进行进一步的处理。  

|异常消息|
|------------------------|
|库异常。|
|库异常: {exception}。|
|未知库异常: {exception}。 {customer_support_guidance}。|

