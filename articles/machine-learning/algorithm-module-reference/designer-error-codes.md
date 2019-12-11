---
title: 模块错误疑难解答
titleSuffix: Azure Machine Learning
description: 使用错误代码对 Azure 机器学习设计器中的模块异常进行故障排除
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 12/03/2019
ms.openlocfilehash: d491a67a44a2cb01e4968c4d31d20dee4b75a60a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980278"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>设计器的异常和错误代码（预览版）

本文介绍 Azure 机器学习设计器（预览版）中的错误消息和异常代码，以帮助你对机器学习管道进行故障排除。

可以通过两种方法获取设计器中的错误消息的完整文本：  

- 单击右窗格中的 "**查看输出日志**" 链接，并滚动到底部。 详细的错误消息将显示在窗口的最后两行中。  
  
- 选择出现错误的模块，然后单击红色 X。只显示相关的错误文本。

## <a name="error-0001"></a>错误 0001  
 如果找不到数据集的一个或多个指定列，则会发生异常。  

 如果对模块进行列选择，但所选列在输入数据集中不存在，则会收到此错误。 如果已手动键入列名或列选择器提供了在运行管道时数据集中不存在的建议列，则可能出现此错误。  

**解决方法：** 重新访问引发此异常的模块，并验证列名或名称是否正确，以及所有引用的列是否都存在。  

|异常消息|
|------------------------|
|找不到一个或多个指定的列。|
|找不到名称或索引为 "{column_id}" 的列。|
|"{Arg_name_missing_column}" 中不存在名为或索引为 "{column_id}" 的列。|
|"{Arg_name_missing_column}" 中不存在名为 "{column_id}" 的列，但存在于 "{arg_name_has_column}" 中。|


## <a name="error-0002"></a>错误 0002  
 如果一个或多个参数无法分析或者无法从指定的类型转换成所需的目标方法类型，则会发生异常。  

 当你将参数指定为输入并且值类型与预期的类型不同，并且无法执行隐式转换时，Azure 机器学习中会出现此错误。  

**解决方法：** 检查模块要求并确定所需的值类型（string、integer、double 等）  

|异常消息|
|------------------------|
|未能分析参数。|
|未能分析 "{arg_name_or_column}" 参数。|
|未能将 "{arg_name_or_column}" 参数转换为 "{to_type}"。|
|未能将 "{arg_name_or_column}" 参数从 "{from_type}" 转换为 "{to_type}"。|
|未能将 "{arg_name_or_column}" 参数值 "{arg_value}" 从 "{from_type}" 转换为 "{to_type}"。|
|无法将列 "{arg_name_or_column}" 中的值 "{arg_value}" 从 "{from_type}" 转换为 "{to_type}"，格式为 "{bcp.fmt}"。|


## <a name="error-0003"></a>错误 0003  
 如果一个或多个输入为 null 或为空，则会发生异常。  

 如果模块的任何输入或参数为 null 或为空，则将在 Azure 机器学习中收到此错误。  例如，如果未键入参数的任何值，则可能会发生此错误。 如果选择了包含缺失值的数据集或空数据集，也可能会发生这种情况。  

**解决方法：**

+ 打开生成异常的模块，并验证是否已指定所有输入。 确保指定了所有必需的输入。 
+ 请确保可以访问从 Azure 存储加载的数据，并且不会更改帐户名称或密钥。  
+ 检查输入数据中是否缺少值或 null 值。
+ 如果对数据源使用查询，请验证数据是否以您期望的格式返回。 
+ 检查数据规范中的拼写错误或其他更改。
  
|异常消息|
|------------------------|
|一个或多个输入为 null 或为空。|
|输入 "{0}" 为 null 或为空。|


## <a name="error-0004"></a>错误 0004  
 如果参数小于或等于特定值，则会发生异常。  

 如果消息中的参数低于用于处理数据的模块所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发异常的模块，并将参数修改为大于指定的值。  

|异常消息|
|------------------------|
|参数应大于边界值。|
|参数 "{0}" 值应大于 {1}。|
|参数 "{0}" 的值为 "{1}"，该值应大于 {2}。|


## <a name="error-0005"></a>错误 0005  
 如果参数小于特定值，则会发生异常。  

 如果消息中的参数低于或等于模块处理数据所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发异常的模块，并将参数修改为大于或等于指定值。  

|异常消息|
|------------------------|
|参数应大于或等于边界值。|
|参数 "{arg_name}" 值应大于或等于 {target_val}。|
|参数 "{arg_name}" 的 true_val 值应大于或等于 {target_val}。|


## <a name="error-0006"></a>错误 0006  
 如果参数为大于或等于指定值，则会发生异常。  

 如果消息中的参数大于或等于用于处理数据的模块所需的边界值，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 重新访问引发异常的模块，并将参数修改为小于指定值。  

|异常消息|
|------------------------|
|参数不匹配。 一个参数应小于另一个参数。|
|参数 "{0}" 值应小于参数 "{1}" 值。|
|参数 "{0}" 的值应小于 {2}的值 "{1}"。|


## <a name="error-0007"></a>错误 0007  
 如果参数大于特定值，则会发生异常。  

 如果在模块的属性中指定的值大于允许的值，则将在 Azure 机器学习中收到此错误。 例如，您可以指定不在受支持的日期范围之外的数据，也可以指示在只有三列可用时使用五行。 

 如果您指定的两个数据集需要以某种方式匹配，也可能会看到此错误。 例如，如果您要重命名列，并按索引指定列，则您提供的名称数必须与列索引的数目匹配。 另一个示例可能是使用两列的数学运算，其中的列必须具有相同的行数。 

**解决方法：**

 + 打开相关的模块并查看任何数值属性设置。
 + 确保任何参数值都在该属性的支持值范围内。
 + 如果该模块采用多个输入，请确保输入的大小相同。
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 检查数据集或数据源是否已更改。 有时，在列数、列数据类型或数据的大小发生更改后，使用以前版本的数据的值将失败。  

|异常消息|
|------------------------|
|参数不匹配。 一个参数应小于或等于另一个参数。|
|参数 "{0}" 的值应小于或等于参数 "{1}" 值。|
|参数 "{0}" 的值应小于或等于 {2}的值 "{1}"。|


## <a name="error-0008"></a>错误 0008  
 如果参数不在范围内，则会发生异常。  

 如果消息中的参数超出了模块处理数据所需的界限，则会在 Azure 机器学习中收到此错误。  

 例如，如果您尝试使用 "[添加行](add-rows.md)" 来合并具有不同列数的两个数据集，则会显示此错误。  

**解决方法：** 重新访问引发异常的模块，并将参数修改为在指定的范围内。  

|异常消息|
|------------------------|
|参数字不在指定的范围内。|
|参数 "{arg_name}" 的值不在范围内。|
|参数 "{arg_name}" 的值应在 [{a}，{b}] 的范围内。|
|参数 "{arg_name}" 的值不在范围内。在于|


## <a name="error-0009"></a>错误 0009  
 如果错误地指定了 Azure 存储帐户名称或容器名称，则会出现异常。  

当你为 Azure 存储帐户指定参数，但无法解析名称或密码时，Azure 机器学习设计器中会出现此错误。 密码或帐户名称错误的发生可能有多种原因：

 + 帐户的类型错误。 某些新的帐户类型不支持与机器学习设计器一起使用。 有关详细信息，请参阅[导入数据](import-data.md)。
 + 你输入了不正确的帐户名
 + 帐户已不存在
 + 存储帐户的密码错误或已更改
 + 你未指定容器名称，或者该容器不存在
 + 你未完全指定文件路径（blob 的路径）
   

**解决方法：**

当你尝试手动输入帐户名称、密码或容器路径时，通常会出现此类问题。 建议为[导入数据](import-data.md)模块使用新向导，这有助于查找和检查名称。

还要检查帐户、容器或 blob 是否已删除。 使用另一个 Azure 存储实用工具验证是否正确输入了帐户名和密码，以及该容器是否存在。 

Azure 机器学习不支持某些较新的帐户类型。 例如，新的 "热" 或 "冷" 存储类型不能用于机器学习。 经典存储帐户和创建为 "常规用途" 的存储帐户都可以正常工作。

如果已指定 blob 的完整路径，请验证该路径是否指定为**container/blobname**，以及该帐户中是否存在容器和 blob。  

 路径不应包含前导反斜杠。 例如， **/container/blob**不正确，应输入为**容器/blob**。  


|异常消息|
|------------------------|
|Azure 存储帐户名称或容器名称不正确。|
|Azure 存储帐户名称 "{0}" 或容器名称 "{1}" 不正确;应为容器/blob 格式的容器名称。|


## <a name="error-0010"></a>错误 0010  
 如果输入数据集的列名应匹配但却没有匹配，则会发生异常。  

 如果消息中的列索引在两个输入数据集中具有不同的列名称，您将在 Azure 机器学习中收到此错误。  

**解决方法：** 使用 "[编辑元数据](edit-metadata.md)" 或修改原始数据集，使指定的列索引具有相同的列名称。  

|异常消息|
|------------------------|
|输入数据集中具有相应索引的列具有不同的名称。|
|对于输入数据集的列 {0} （从零开始）（分别为{1} 和 {2}），列名称不同。|


## <a name="error-0011"></a>错误 0011  
 如果传递的列集参数不适用于任何数据集列，则会发生异常。  

 如果指定的列选择与给定数据集中的任何列都不匹配，则将在 Azure 机器学习中收到此错误。  

 如果尚未选择列并且需要至少一个列以便模块正常工作，则也可能会出现此错误。  

**解决方法：** 修改该模块中的列选择，以便将其应用于数据集中的列。  

 如果该模块要求你选择特定列（如标签列），请验证是否选择了正确的列。  

 如果选择了不适当的列，请将其删除，然后重新运行管道。  

|异常消息|
|------------------------|
|指定的列集不适用于任何数据集列。|
|指定的列集 "{0}" 不适用于任何数据集列。|


## <a name="error-0012"></a>错误 0012  
 如果类的实例无法使用传递的参数集创建，则会发生异常。  

**解决方法：** 此错误不是由用户操作的，将在未来版本中弃用。  

|异常消息|
|------------------------|
|模型未训练，请先训练模型。|
|训练的模型（{arg_name}）使用训练的模型。|


## <a name="error-0013"></a>错误 0013  
 如果传递到模块的学习器是无效类型，则会发生异常。  

 当定型模型与连接计分模块不兼容时，会发生此错误。 <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**解决方法：**

确定培训模块生成的学习器的类型，并确定适用于学习器的评分模块。 

如果模型是使用任何专用培训模块训练的，请将训练的模型仅连接到相应的专用计分模块。 


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
|学习器 "{arg_name}" 具有无效的类型。|
|学习器 "{arg_name}" 具有无效的类型 "{learner_type}"。|


## <a name="error-0014"></a>错误 0014  
 如果列的唯一值计数大于允许值，则会出现异常。  

 当列包含的唯一值太多时，将发生此错误。  例如，如果您指定某个列作为分类数据进行处理，并且列中的唯一值太多，以允许处理完成，则您可能会看到此错误。 如果两个输入中的唯一值的数目不匹配，也可能会看到此错误。   

**解决方法：**

打开生成错误的模块，并标识用作输入的列。 对于某些模块，您可以右键单击数据集输入，然后选择 "**可视化**" 以获取各个列的统计信息，包括唯一值的数目及其分布。

对于打算用于分组或分类的列，请采取措施减少列中的唯一值的数目。 您可以根据列的数据类型，以不同的方式减小。 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 找不到与你的方案匹配的解决方案？ 您可以提供有关本主题的反馈，其中包括生成错误的模块的名称，以及列的数据类型和基数。 我们将使用该信息为常见方案提供更有针对性的故障排除步骤。   

|异常消息|
|------------------------|
|列唯一值数大于允许值。|
|列中的唯一值数： "{column_name}" 大于允许值。|
|列中的唯一值数： "{column_name}" 超出了 {限制} 的元组计数。|


## <a name="error-0015"></a>错误 0015  
 如果数据库连接失败，则会发生异常。  

 如果您输入了不正确的 SQL 帐户名称、密码、数据库服务器或数据库名称，或者由于数据库或服务器问题而无法建立与数据库的连接，则您将收到此错误。  

**解决方法：** 验证是否正确输入了帐户名称、密码、数据库服务器和数据库，以及指定的帐户是否具有正确的权限级别。 验证数据库当前是否可访问。  

|异常消息|
|------------------------|
|建立数据库连接时出错。|
|建立数据库连接时出错： {0}。|


## <a name="error-0016"></a>错误 0016  
 如果传递到模块的输入数据集应具有兼容的列类型但却没有，则会发生异常。  

 如果在两个或更多个数据集中传递的列的类型不兼容，则将在 Azure 机器学习中收到此错误。  

**解决方法：** 使用 "[编辑元数据](edit-metadata.md)" 或修改原始输入数据集<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 以确保列的类型兼容。  

|异常消息|
|------------------------|
|输入数据集中具有相应索引的列使用了不兼容的类型。|
|定型数据和测试数据之间的列 "{first_col_names}" 不兼容。|
|列 "{first_col_names}" 和 "{second_col_names}" 不兼容。|
|列元素类型与输入数据集的列 "{first_col_names}" （从零开始）（分别为 {first_dataset_names} 和 {second_dataset_names}）不兼容。|


## <a name="error-0017"></a>错误 0017  
 如果所选列使用当前模块不支持的数据类型，则会发生异常。  

 例如，如果您的列选择包含数据类型不能由模块处理的列（例如，数学运算的字符串列或分类特征列的分数列），则可能会在 Azure 机器学习收到此错误。必填。  

**解决方法：**
 1. 确定出现问题的列。
 2. 查看模块的要求。
 3. 修改列以使其符合要求。 你可能需要使用以下几个模块来做出更改，具体取决于所尝试的列和转换：
    + 使用 "[编辑元数据](edit-metadata.md)" 可以更改列的数据类型，或将列用法从功能更改为数值，将分类更改为非分类等。
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. 作为最后的手段，你可能需要修改原始输入数据集。

> [!TIP]
> 找不到与你的方案匹配的解决方案？ 您可以提供有关本主题的反馈，其中包括生成错误的模块的名称，以及列的数据类型和基数。 我们将使用该信息为常见方案提供更有针对性的故障排除步骤。 

|异常消息|
|------------------------|
|无法处理当前类型的列。 模块不支持该类型。|
|无法处理 {col_type} 类型的列。 模块不支持该类型。|
|无法处理 col_type {0} 类型的列 "{col_name}"。 模块不支持该类型。|
|无法处理 col_type {0} 类型的列 "{col_name}"。 模块不支持该类型。 参数名称： {arg_name}。|


## <a name="error-0018"></a>错误 0018  
 如果输入数据集无效，则会发生异常。  

**解决方法：** Azure 机器学习中的此错误可能出现在多个上下文中，因此没有单个解决方法。 通常，此错误表示作为模块输入提供的数据具有错误的列数，或者数据类型不符合模块的要求。 例如：  

-   模块需要标签列，但没有列标记为标签，或者尚未选择标签列。  
  
-   该模块要求数据进行分类，但你的数据是数值。  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   数据的格式不正确。  
  
-   导入的数据包含无效字符、错误值或超出范围值。  
-   此列为空或包含的缺失值太多。  

 若要确定要求和数据的可能方式，请查看将使用数据集作为输入的模块的帮助主题。  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|异常消息|
|------------------------|
|数据集无效。|
|{dataset1} 包含无效数据。|
|{dataset1} 和 {dataset2} 应为一致性一致。|
|{dataset1} 包含无效数据，{reason}。|
|{dataset1} 包含 {invalid_data_category}。 {troubleshoot_hint}|


## <a name="error-0019"></a>错误 0019  
 如果列应包含排序的值但并非如此，则会发生异常。  

 如果指定的列值不按序显示，您将在 Azure 机器学习中收到此错误。  

**解决方法：** 通过手动修改输入数据集并重新运行模块来对列值进行排序。  

|异常消息|
|------------------------|
|列中的值未排序。|
|列 "{0}" 中的值未排序。|
|数据集 "{1}" 的列 "{0}" 中的值未排序。|


## <a name="error-0020"></a>错误 0020  
 如果某些数据集中传递给模块的列数太小，则会发生异常。  

 如果没有为模块选择足够的列，您将在 Azure 机器学习中收到此错误。  

**解决方法：** 请重新访问该模块，并确保列选择器选择了正确的列数。  

|异常消息|
|------------------------|
|输入数据集中的列数小于允许的最小列数。|
|输入数据集中的列数小于允许的最小值 {required_columns_count} 列。|
|输入数据集 "{arg_name}" 中的列数小于允许的最小值 {required_columns_count} 列。|


## <a name="error-0021"></a>错误 0021  
 如果某些数据集中传递给模块的行数太小，则会发生异常。  

 如果数据集中的行不足，无法执行指定的操作，则在 Azure 机器学习中会出现此错误。 例如，如果输入数据集为空，或者您尝试执行的操作需要的最小行数为有效，则您可能会看到此错误。 此类操作可以包括（但不限于）基于统计方法、特定类型的装箱和了解计数的分组或分类。  

**解决方法：**

 + 打开返回错误的模块，然后检查输入数据集和模块属性。 
 + 验证输入数据集是否不为空，以及是否有足够的数据行可满足模块帮助中所述的要求。  
 + 如果从外部源加载数据，请确保数据源可用并且数据定义中没有错误或更改，这会导致导入过程获取更少的行。
 + 如果要对模块的上游数据执行操作，而这些数据可能会影响数据类型或值的数量（如清除、拆分或联接操作），请检查这些操作的输出以确定返回的行数。  

|异常消息|
|------------------------|
|输入数据集中的行数小于允许的最小行数。|
|输入数据集中的行数小于允许的最小值 {required_rows_count} 行。|
|输入数据集中的行数小于允许的最小值 {required_rows_count} 行。 在于|
|输入数据集中的行数 "{arg_name}" 小于允许的最小值 {required_rows_count} 行。|
|输入数据集 "{arg_name}" 中的行数为 {actual_rows_count}，小于允许的最小值 {required_rows_count} 行。|
|输入数据集 "{arg_name}" 中的 "{row_type}" 行数为 {actual_rows_count}，小于允许的最小值 {required_rows_count} 行。|


## <a name="error-0022"></a>错误 0022  
 如果输入数据集中的选定列数不等于预期数量，则会发生异常。  

 当下游模块或操作需要特定数量的列或输入，并且您提供的列或输入太少或过多时，可能会出现 Azure 机器学习中的错误。 例如：  

-   您可以指定单个标签列或键列，并且不小心选择了多个列。  
  
-   正在重命名列，但提供的名称多于或少于列的名称。  
  
-   源或目标中的列数已更改或与模块使用的列数不匹配。  
  
-   你为输入提供了以逗号分隔的值列表，但值的数目不匹配，或者不支持多个输入。  

**解决方法：** 请重新访问该模块并检查列选择，以确保选择正确的列数。 验证上游模块的输出和下游操作的要求。  

 如果使用了一种可选择多个列的列选择选项（列索引、所有功能、所有数字等），则会验证所选内容返回的列数是否准确。  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 验证上游列的数量或类型是否未更改。  

 如果使用建议的数据集来训练模型，请记住，推荐器需要的列数有限，与用户项对或用户-项排名相关。 在训练模型或拆分建议数据集之前，请删除其他列。 有关详细信息，请参阅[拆分数据](split-data.md)。  

|异常消息|
|------------------------|
|在输入数据集中选择的列数不等于所需数目。|
|输入数据集中的选定列数不等于 {0}。|
|列选择模式 "{0}" 提供的输入数据集中的选定列数不等于 {1}。|
|列选择模式 "{0}" 应提供输入数据集中所选 {1} 列，但实际提供 {2} 列。|


## <a name="error-0023"></a>错误 0023  

如果输入数据集的目标列不能用于当前训练器模块，则会发生异常。  

如果目标列（如模块参数中所选的）不是有效的数据类型，包含所有缺少的值或未按预期进行分类，则会发生此 Azure 机器学习错误。  

**解决方法：** 请重新访问模块输入以检查标签/目标列的内容。 请确保它没有所有缺失值。 如果该模块要求目标列是分类列，请确保目标列中有多个非重复值。  

|异常消息|
|------------------------|
|输入数据集包含不支持的目标列。|
|输入数据集包含不支持的目标列 "{0}"。|
|对于 {1}类型的学习器，输入数据集包含不支持的目标列 "{0}"。|


## <a name="error-0024"></a>错误 0024  
如果数据集不包含标签列，则会发生异常。  

 当模块需要标签列并且数据集没有标签列时，将发生 Azure 机器学习中的错误。 例如，评估已评分的数据集通常需要标签列来计算准确性指标。  

还可能出现标签列存在于数据集中，但 Azure 机器学习未正确检测。

**解决方法：**

+ 打开生成错误的模块，确定标签列是否存在。 列的名称或数据类型并不重要，只要该列包含您尝试预测的单个结果（或依赖变量）即可。 如果不确定哪个列具有标签，请查找一般名称，如 "*类*" 或 "*目标*"。 
+  如果数据集不包含标签列，则可能是显式或意外删除了上游的标签列。 这也可能是因为数据集不是上游计分模块的输出。
+ 若要将列显式标记为标签列，请添加[编辑元数据](edit-metadata.md)模块并连接数据集。 仅选择 "标签" 列，然后从 "**字段**" 下拉列表中选择 "**标签**"。 
+ 如果选择了错误的列作为标签，可以从**字段**中选择 "**清除标签**" 以修复列中的元数据。 
  
|异常消息|
|------------------------|
|数据集中没有标签列。|
|"{Dataset_name}" 中没有标签列。|


## <a name="error-0025"></a>错误 0025  
 如果数据集不包含评分列，则会发生异常。  

 如果计算模型的输入不包含有效的评分列，则会在 Azure 机器学习中出现此错误。 例如，用户在使用正确训练的模型对数据集进行评分之前，或在将分数列显式拖出上游之前，会尝试对其进行评估。 如果两个数据集上的评分列不兼容，也会发生此异常。 例如，你可能尝试将线性回归量的准确性与二进制分类器进行比较。  

**解决方法：** 重新访问评估模型的输入，并检查它是否包含一个或多个评分列。 如果不是，则不会对数据集进行评分，或将评分列放在上游模块中。  

|异常消息|
|------------------------|
|数据集中没有评分列。|
|"{0}" 中没有评分列。|
|"{1}" 生成的 "{0}" 中没有评分列。 请使用正确类型的学习器为数据集评分。|


## <a name="error-0026"></a>错误 0026  
 如果不允许使用具有相同名称的列，则会出现异常。  

 如果多个列具有相同的名称，则 Azure 机器学习发生此错误。 你可能会收到此错误的一种方法是，数据集不具有标题行和列名称自动分配： Col0、Col1 等。  

**解决方法：** 如果列具有相同的名称，请在输入数据集和模块之间插入一个[编辑元数据](edit-metadata.md)模块。 使用[编辑元数据](edit-metadata.md)中的列选择器选择要重命名的列，在 "**新列名**" 文本框中键入新名称。  

|异常消息|
|------------------------|
|在参数中指定了相同的列名。 模块不允许相同的列名。|
|不允许参数 "{0}" 和 "{1}" 中的列名称相等。 请指定不同的名称。|


## <a name="error-0027"></a>错误 0027  
 如果两个对象的大小必须相同但却不同，则会发生异常。  

 这是 Azure 机器学习中的常见错误，这可能是由许多情况引起的。  

**解决方法：** 没有特定的解决方法。 不过，您可以检查以下情况：  

-   如果要重命名列，请确保每个列表（输入列和新名称列表）具有相同数量的项。  
  
-   如果要联接或连接两个数据集，请确保它们具有相同的架构。  
  
-   如果要联接具有多个列的两个数据集，请确保键列具有相同的数据类型，并选择选项 "**允许重复项并保留列顺序**"。  

|异常消息|
|------------------------|
|传递的对象大小不一致。|
|"{0}" 的大小与 "{1}" 的大小不一致。|


## <a name="error-0028"></a>错误 0028  
 如果列集包含重复的列名，而这是不允许的，则会发生异常。  

 如果列名称重复，则会出现 Azure 机器学习中的此错误。也就是说，不是唯一的。  

**解决方法：** 如果任何列具有相同的名称，请在输入数据集和引发错误的模块之间添加[编辑元数据](edit-metadata.md)的实例。 使用[编辑元数据](edit-metadata.md)中的列选择器选择要重命名的列，然后在 "**新列名**" 文本框中键入新的列名称。 如果要重命名多个列，请确保在**新列名称**中键入的值是唯一的。  

|异常消息|
|------------------------|
|列集包含重复的列名。|
|名称 "{duplicated_name}" 是重复的。|
|名称 "{duplicated_name}" 在 "{arg_name}" 中重复。|
|名称 "{duplicated_name}" 是重复的。 详细信息： {details}|


## <a name="error-0029"></a>错误 0029  
 如果传递的 URI 无效，则会发生异常。  

 如果传递的 URI 无效，则 Azure 机器学习发生此错误。  如果满足以下任一条件，则会收到此错误：  

-   为读取或写入而提供的用于 Azure Blob 存储的公共或 SAS URI 包含错误。  
  
-   SAS 的时间范围已过期。  
  
-   通过 HTTP 源的 Web URL 表示文件或环回 URI。  
  
-   通过 HTTP 的 Web URL 包含格式不正确的 URL。  
  
-   远程源无法解析 URL。  

**解决方法：** 重新访问该模块并验证 URI 的格式。 如果数据源是通过 HTTP 的 Web URL，请验证所需的源不是文件或环回 URI （localhost）。  

|异常消息|
|------------------------|
|传递了无效的 URI。|
|Uri "{0}" 无效。|


## <a name="error-0030"></a>错误 0030  
 如果无法下载文件，则会发生异常。  

 如果无法下载文件，则会在 Azure 机器学习发生此异常。 如果在三次重试后尝试从 HTTP 源读取失败，将会收到此异常。  

**解决方法：** 验证 HTTP 源的 URI 是否正确，以及该站点当前是否可通过 Internet 访问。  

|异常消息|
|------------------------|
|无法下载文件。|
|下载文件时出错： {0}。|


## <a name="error-0031"></a>错误 0031  
 如果列集中的列数小于所需列数，则会发生异常。  

 如果选择的列数小于所需的列数，则会发生 Azure 机器学习中的错误。  如果未选择所需的最小列数，则会收到此错误。  

**解决方法：** 使用**列选择器**将其他列添加到列选择。  

|异常消息|
|------------------------|
|列集中的列数小于所需列数。|
|应至少指定 {0} 列。 指定的实际列数为 {1}。|


## <a name="error-0032"></a>错误 0032  
 如果参数不是数字，则会发生异常。  

 如果参数为 double 或 NaN，则会在 Azure 机器学习中收到此错误。  

**解决方法：** 修改指定的参数以使用有效的值。  

|异常消息|
|------------------------|
|参数不是数字。|
|"{0}" 不是数字。|


## <a name="error-0033"></a>错误 0033  
 如果参数无穷大，则会发生异常。  

 如果参数是无限的，则会发生此错误 Azure 机器学习。 如果参数 `double.NegativeInfinity` 或 `double.PositiveInfinity`，则会收到此错误。  

**解决方法：** 将指定的参数修改为有效的值。  

|异常消息|
|------------------------|
|参数必须是有限的。|
|"{0}" 不是有限的。|


## <a name="error-0034"></a>错误 0034  
 如果给定的用户项对存在多个分级，则会发生异常。  

 如果用户-项目对具有多个分级值，则 Azure 机器学习中会出现此错误。  

**解决方法：** 请确保用户-项对只拥有一个分级值。  

|异常消息|
|------------------------|
|数据集中的值存在多个分级。|
|分级预测数据表中的用户 {user} 和 item {item} 有多个评级。|
|{Dataset} 中的用户 {user} 和 item {item} 有多个分级。|


## <a name="error-0035"></a>错误 0035  
 如果没有为给定的用户或项提供任何特征，则会发生异常。  

 如果你尝试使用建议模型进行评分，但找不到功能矢量，则 Azure 机器学习中会出现此错误。  

**解决方法：**

使用项目功能或用户功能时，必须满足 Matchbox 推荐器的某些要求。  此错误表示你作为输入提供的用户或项缺少功能矢量。 确保每个用户或项目的数据中都有一条功能矢量。  

 例如，如果你使用用户的年龄、位置或收入等功能对推荐模型进行了定型，但现在想要为在训练过程中未见到的新用户创建评分，则必须提供一组等效功能（即年龄、位置和收益值），以便为新用户进行适当的预测。 

 如果没有针对这些用户的任何功能，请考虑使用功能设计来生成相应的功能。  例如，如果您没有单独的用户年龄或收入值，则可能会生成用于一组用户的近似值。 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 解决方法不适用于您的案例？ 欢迎在本文中发送反馈，并提供有关该方案的信息，包括模块和列中的行数。 我们将使用此信息在将来提供更详细的故障排除步骤。

|异常消息|
|------------------------|
|没有为所需的用户或项目提供特征。|
|需要 {0} 功能，但未提供。|


## <a name="error-0036"></a>错误 0036  
 如果为给定的用户或项提供了多个特征向量，则会发生异常。  

 如果多次定义了特性向量，则会出现 Azure 机器学习中的此错误。  

**解决方法：** 确保功能矢量未定义多次。  

|异常消息|
|------------------------|
|用户或项存在重复的特征定义。|
|{0}的复制功能定义。|


## <a name="error-0037"></a>错误 0037  
 如果指定了多个标签列但只允许一个，则会发生异常。  

 如果选择了多个列作为新标签列，则会出现 Azure 机器学习中的错误。 大多数监督式学习算法要求将单个列标记为目标或标签。  

**解决方法：** 请确保选择单个列作为新的标签列。  

|异常消息|
|------------------------|
|指定了多个标签列。|
|在 "{dataset_name}" 中指定了多个标签列。|


## <a name="error-0039"></a>错误 0039  
 如果操作失败，则会发生异常。  

 当无法完成内部操作时，会发生 Azure 机器学习中的此错误。  

**解决方法：** 此错误是由许多条件引起的，并且没有特定的补救措施。  
 下表包含此错误的一般消息，后跟特定的条件说明。 

 如果没有可用的详细信息，请[发送反馈](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning)，并提供有关生成错误和相关条件的模块的信息。

|异常消息|
|------------------------|
|操作失败。|
|完成操作时出错： "{failed_operation}"。|
|完成操作时出错： "{failed_operation}"。 原因： "{reason}"。|


## <a name="error-0042"></a>错误 0042  
 如果无法将列转换为其他类型，则会发生异常。  

 如果无法将列转换为指定类型，则 Azure 机器学习发生此错误。  如果模块需要特定数据类型（例如 datetime、text、浮点数或整数），但不能将现有列转换为所需的类型，则将收到此错误。  

例如，您可以选择一个列，然后尝试将其转换为数值数据类型，以用于数学运算，如果该列包含无效数据，则会收到此错误。 

另一个原因是，如果您尝试将包含浮点数或许多唯一值的列用作分类列，则可能会收到此错误。 

**解决方法：**

+ 打开生成错误的模块的帮助页，并验证数据类型要求。
+ 查看输入数据集中的列的数据类型。
+ 检查来自所谓的无架构数据源的数据。
+ 检查数据集的缺失值或特殊字符，它们可能会阻止转换为所需的数据类型。 
    + 数值数据类型应一致：例如，检查整数列中的浮点数。
    + 查找数字列中的文本字符串或 NA 值。 
    + 根据所需的数据类型，可以将布尔值转换为适当的表示形式。
    + 检查文本列中的非 unicode 字符、制表符或控制字符
    + Datetime 数据应保持一致，以避免出现错误，但清理可能会由于多种格式。 请考虑使用 <!--the [Execute R Script](execute-r-script.md) or -->[执行 Python 脚本](execute-python-script.md)模块以执行清理。  
+ 如有必要，请修改输入数据集中的值，以便可以成功转换列。 修改可能包括装箱、截断或舍入操作、消除离群值或缺少值的插补法。 请参阅以下文章，了解机器学习中的一些常见数据转换方案：
    + [清理缺失数据](clean-missing-data.md)
    + [规范化数据](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 解决不清楚或不适合您的情况？ 欢迎在本文中发送反馈，并提供有关该方案的信息，包括模块和列的数据类型。 我们将使用此信息在将来提供更详细的故障排除步骤。  

|异常消息|
|------------------------|
|不允许转换。|
|无法将 {0} 类型的列转换为类型 {1}的列。|
|无法将类型 {0} 的列 "{2}" 转换为类型 {1}的列。|
|无法将类型 {0} 的列 "{2}" 转换为类型 {1}的列 "{3}"。|


## <a name="error-0044"></a>错误 0044  
 如果无法从现有值派生列的元素类型，则会发生异常。  

 如果无法推断数据集中的一个或多个列的类型，则会出现 Azure 机器学习中的此错误。 连接两个或多个具有不同元素类型的数据集时通常会发生这种情况。 如果 Azure 机器学习无法确定能够表示一列或多列中的所有值而不丢失信息的通用类型，它将生成此错误。  

**解决方法：** 确保两个数据集中的给定列中的所有值都是相同的类型（numeric、Boolean、分类、字符串、日期等）或可以强制转换为相同的类型。  

|异常消息|
|------------------------|
|无法派生列的元素类型。|
|无法为列 "{0}" 派生元素类型-所有元素都是 null 引用。|
|无法为数据集 "{1}" 的列 "{0}" 派生元素类型-所有元素都是 null 引用。|


## <a name="error-0045"></a>错误 0045  
 如果因源中存在混合的元素类型而无法创建列，则会发生异常。  

 当要组合的两个数据集的元素类型不同时，将生成 Azure 机器学习中的此错误。  

**解决方法：** 确保两个数据集中的给定列中的所有值都具有相同的类型（numeric、Boolean、分类、字符串、日期等）。  

|异常消息|
|------------------------|
|无法创建具有混合元素类型的列。|
|无法创建混合元素类型的 id 为 "{column_id}" 的列： \ n\tType data [{row_1}，{column_id}] 是 "{type_1}"。 数据类型 [{row_2}，{column_id}] 为 "{type_2}"。|
|无法创建混合元素类型为 "{column_id}" 的列：块区 {chunk_id_1} 中的 \ n\tType 为 "{type_1}"。 块区 {chunk_id_2} 中的类型为 "{type_2}"，区块大小为： {chunk_size}。|


## <a name="error-0046"></a>错误 0046  
 如果无法在指定路径上创建目录，则会发生异常。  

 当无法在指定路径上创建目录时，会发生 Azure 机器学习中的错误。 如果 Hive 查询的输出目录的路径部分不正确或不可访问，则会收到此错误。  

**解决方法：** 请重新访问该模块，并验证该目录路径的格式是否正确，以及当前凭据是否可访问该路径。  

|异常消息|
|------------------------|
|请指定有效的输出目录。|
|目录：无法创建 {0}。 请指定有效路径。|


## <a name="error-0047"></a>错误 0047  
 如果某些数据集中传递给模块的特征列数太小，则会发生异常。  

 如果用于定型的输入数据集不包含算法所需的最小列数，则会发生 Azure 机器学习中的错误。 通常，数据集为空或仅包含定型列。  

**解决方法：** 请重新访问输入数据集，以确保除了标签列之外还有一个或多个其他列。  

|异常消息|
|------------------------|
|输入数据集中的特征列数小于允许的最小数目。|
|输入数据集中的特征列数小于允许的最小值 {required_columns_count} 列。|
|输入数据集中的特征列数 "{arg_name}" 小于允许的最小值 {required_columns_count} 列。|


## <a name="error-0048"></a>错误 0048  
 如果无法打开文件，则会发生异常。  

 如果无法打开文件进行读取或写入，则会出现 Azure 机器学习中的错误。 由于以下原因，你可能会收到此错误：  

-   容器或文件（blob）不存在  
  
-   文件或容器的访问级别不允许访问该文件  
  
-   文件太大，无法读取或格式不正确  

**解决方法：** 重新访问该模块和您尝试读取的文件。  

 验证容器和文件的名称是否正确。  

 使用 Azure 经典门户或 Azure 存储工具验证你是否有权访问该文件。  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|异常消息|
|------------------------|
|无法打开文件。|
|打开文件时出错： {0}。|
|打开文件时出错： {0}。 存储异常消息： {1}。|


## <a name="error-0049"></a>错误 0049  
 如果无法解析文件，则会发生异常。  

 如果无法分析文件，则会发生此错误 Azure 机器学习。 如果在[导入数据](import-data.md)模块中选择的文件格式与文件的实际格式不匹配，或者如果文件包含无法识别的字符，则会收到此错误。  

**解决方法：** 如果与文件格式不匹配，请重新访问该模块并更正文件格式选择。 如果可能，请检查文件以确认它不包含任何非法字符。  

|异常消息|
|------------------------|
|无法分析文件。|
|分析 {file_format} 文件时出错。|
|分析 {file_format} 文件时出错： {file_name}。|
|分析 {file_format} 文件时出错。 原因： {failure_reason}。|
|分析 {file_format} 文件时出错： {file_name}。 原因： {failure_reason}。|


## <a name="error-0052"></a>错误 0052  
 如果错误地指定了 Azure 存储帐户密钥，则会发生异常。  

 如果用于访问 Azure 存储帐户的密钥不正确，则会发生此 Azure 机器学习错误。 例如，如果 Azure 存储密钥在复制和粘贴时被截断，或者使用了错误的密钥，则可能会看到此错误。  

 有关如何获取 Azure 存储帐户的密钥的详细信息，请参阅[查看、复制和重新生成存储访问密钥](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)。  

**解决方法：** 重新访问该模块，并验证 Azure 存储密钥对于该帐户是正确的;必要时，请从 Azure 经典门户重新复制密钥。  

|异常消息|
|------------------------|
|Azure 存储帐户密钥不正确。|


## <a name="error-0053"></a>错误 0053  
 当没有用户功能或项可用于 matchbox 建议时，将发生异常。  

 当找不到功能向量时，将生成 Azure 机器学习中的此错误。  

**解决方法：** 确保功能矢量出现在输入数据集中。  

|异常消息|
|------------------------|
|需要用户特征或/和项，但未提供。|


## <a name="error-0056"></a>错误 0056  
 如果为操作选择的列违反要求，则会出现异常。  

 如果要为需要特定数据类型的列的操作选择列，则会在 Azure 机器学习中出现此错误。 

 如果该列是正确的数据类型，则也可能发生此错误，但您正在使用的模块要求该列也标记为特征列、标签列或分类列。  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**解决方法：**

1.  查看当前选择的列的数据类型。 

2. 确定所选列是否为分类列、标签列或功能列。  
  
3.  查看在其中进行列选择的模块的帮助主题，以确定是否存在对数据类型或列使用情况的特定要求。  
  
3.  使用 "[编辑元数据](edit-metadata.md)" 更改此操作的持续时间内的列类型。 如果需要将列类型更改回其原始值，请使用另一个[编辑元数据](edit-metadata.md)的实例（如果需要用于下游操作）。  

|异常消息|
|------------------------|
|一个或多个选定的列不在允许的类别中。|
|名称为 "{col_name}" 的列不在允许的类别中。|


## <a name="error-0057"></a>错误 0057  
 如果尝试创建的文件或 Blob 已存在，则会发生异常。  

 当您使用[导出数据](export-data.md)模块或其他模块将 Azure 机器学习中的管道的结果保存到 Azure blob 存储，但您尝试创建已存在的文件或 blob 时，将发生此异常。   

**解决方法：**

 仅当之前将属性 " **Azure blob 存储写入模式**" 设置为 "**错误**" 时，才会收到此错误。 按照设计，如果尝试将数据集写入已存在的 blob，此模块将引发错误。

 - 打开模块属性，并将 " **Azure blob 存储写入模式**" 属性更改为 "**覆盖**"。
 - 或者，你可以键入不同的目标 blob 或文件的名称，并确保指定一个尚不存在的 blob。  

|异常消息|
|------------------------|
|文件或 Blob 已存在。|
|文件或 Blob "{0}" 已存在。|


## <a name="error-0058"></a>错误 0058  
 如果数据集不包含所需的标签列，则会出现 Azure 机器学习中的错误。  

 当提供的标签列与学习器所需的数据或数据类型不匹配或具有错误的值时，也会发生此异常。 例如，当定型二元分类器时使用实际值标签列时，将生成此异常。  

**解决方法：** 解决方法取决于所使用的学习器或培训人员，以及数据集中的列的数据类型。 首先，请验证机器学习算法或培训模块的要求。  

 重新访问输入数据集。 验证预期被视为标签的列是否具有您要创建的模型的正确数据类型。  

 检查缺失值的输入，如有必要，请删除或替换它们。  

 如有必要，请添加[编辑元数据](edit-metadata.md)模块，并确保标签列已标记为标签。  

|异常消息|
|------------------------|
|标签列值和评分标签列的值不可比较。|
|"{Dataset_name}" 中的标签列不符合预期。|
|"{Dataset_name}" 和 "{reason}" 中的标签列不符合预期。|
|"{Dataset_name}" 中不应有标签列 "{column_name}"。|
|标签列 "{column_name}" 不应出现在 "{dataset_name}"、{reason} 中。|


## <a name="error-0059"></a>错误 0059  
 如果在列选取器中指定的列索引无法进行解析，则会发生异常。  

 如果无法分析使用列选择器时指定的列索引，则会出现 Azure 机器学习中的错误。  如果列索引的格式无效，则会收到此错误。  

**解决方法：** 修改列索引以使用有效的索引值。  

|异常消息|
|------------------------|
|无法分析一个或多个指定的列索引或索引范围。|
|未能分析列索引或范围 "{0}"。|


## <a name="error-0060"></a>错误 0060  
 如果在列选取器中指定的列范围超出了范围，则会发生异常。  

 当在列选择器中指定超出范围的列范围时，将发生 Azure 机器学习中的错误。 如果列选取器中的列范围不对应于数据集中的列，则将收到此错误。  

**解决方法：** 修改列选取器中的列范围，使其对应于数据集中的列。  

|异常消息|
|------------------------|
|指定了无效或超出范围的列索引范围。|
|列范围 "{0}" 无效或超出范围。|


## <a name="error-0061"></a>错误 0061  
 如果尝试将行添加到的 DataTable 的列数不同于该表的列数，则会发生异常。  

 如果尝试向数据集添加的行的列数不同于数据集的列数，则会发生此 Azure 机器学习错误。  如果要添加到数据集的行与输入数据集中的列数不同，则您将收到此错误。  如果列数不同，则不能将该行追加到数据集。  

**解决方法：** 修改输入数据集的列数，使其列数与添加的行数相同，或修改添加到的行，使其列数与数据集相同。  

|异常消息|
|------------------------|
|所有表必须具有相同的列数。|
|块区 "{chunk_id_1}" 中的列与块区大小为 {chunk_size} 的块区 "{chunk_id_2}" 不同。|
|文件 "{filename_1}" 中的列计数（计数 = {column_count_1}）与文件 "{filename_2}" （计数 = {column_count_2}）不同。|


## <a name="error-0062"></a>错误 0062  
 尝试比较具有不同学习器类型的两个模型时发生异常。  

 当不能比较两个不同评分数据集的评估指标时，将生成 Azure 机器学习中的此错误。 在这种情况下，不可能比较用于产生两个评分数据集的模型的有效性。  

**解决方法：** 验证评分结果是否由同一种类的机器学习模型生成（二元分类、回归、多类分类、建议、群集、异常检测等）你比较的所有模型都必须具有相同的学习器类型。  

|异常消息|
|------------------------|
|所有模型都必须具有相同的学习器类型。|
|获得不兼容的学习器类型： "{actual_learner_type}"。 预期的学习器类型为： "{expected_learner_type_list}"。|


## <a name="error-0064"></a>错误 0064  
 如果 Azure 存储帐户名称或存储密钥未正确指定，则会发生异常。  

 如果错误地指定了 Azure 存储帐户名称或存储密钥，Azure 机器学习会发生此错误。 如果为存储帐户输入的帐户名称或密码不正确，则会收到此错误。 如果手动输入帐户名或密码，则可能会发生这种情况。 如果帐户已被删除，也可能出现此问题。  

**解决方法：** 验证是否输入了正确的帐户名和密码，以及该帐户是否存在。  

|异常消息|
|------------------------|
|Azure 存储帐户名称或存储密钥不正确。|
|帐户名称的 Azure 存储帐户名称 "{account_name}" 或存储密钥不正确。|


## <a name="error-0065"></a>错误 0065  
 如果错误地指定了 Azure Blob 名称，则会发生异常。  

 如果错误地指定了 Azure blob 名称，则 Azure 机器学习发生此错误。  如果出现以下情况，您将收到错误：  

-   在指定的容器中找不到该 blob。  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   仅在[导入数据](import-data.md)请求中将容器指定为源，但格式为 EXCEL 或 CSV 且带有编码;不允许将容器中所有 blob 的内容串联为这些格式。  
  
-   SAS URI 不包含有效 blob 的名称。  

**解决方法：** 重新访问引发异常的模块。 验证指定的 blob 是否存在于存储帐户中的容器内，以及这些权限是否允许您查看该 blob。 如果你有具有编码格式的 Excel 或 CSV，请验证输入的格式是否为 "**容器名称**"。 验证 SAS URI 是否包含有效 blob 的名称。  

|异常消息|
|------------------------|
|Azure 存储 blob 名称不正确。|
|Azure 存储 blob 名称 "{blob_name}" 不正确。|
|前缀为 "{blob_name_prefix}" 的 Azure 存储 blob 名称不存在。|
|在容器 "{container_name}" 下找不到任何 Azure 存储 blob。|
|找不到通配符路径为 "{blob_wildcard_path}" 的任何 Azure 存储 blob。|


## <a name="error-0066"></a>错误 0066  
 如果无法将某个资源上载到 Azure Blob，则会发生异常。  

 如果无法将资源上载到 Azure Blob，则会发生此 Azure 机器学习错误。  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 两者都保存在包含输入文件的帐户所在的同一 Azure 存储帐户中。  

**解决方法：** 重新访问该模块。 验证 Azure 帐户名称、存储密钥和容器是否正确，以及该帐户是否有权写入该容器。  

|异常消息|
|------------------------|
|无法将资源上载到 Azure 存储空间。|
|无法将文件 "{0}" 作为 "{1}" 上传到 Azure 存储。|


## <a name="error-0067"></a>错误 0067  
 如果数据集的列数不同于预期的列数，则会发生异常。  

 如果数据集的列数不同于预期的列数，则会发生此 Azure 机器学习错误。  如果数据集中的列数与模块在执行期间所需的列数不同，您将收到此错误。  

**解决方法：** 修改输入数据集或参数。  

|异常消息|
|------------------------|
|数据表中存在意外的列数。|
|数据集 "{dataset_name}" 中出现意外的列数。|
|应为 "{expected_column_count}" 列，但找到的是 "{actual_column_count}" 列。|
|在输入数据集 "{dataset_name}" 中，应为 "{expected_column_count}" 列，但找到的是 "{actual_column_count}" 列。|


## <a name="error-0068"></a>错误 0068  
 如果指定的 Hive 脚本不正确，则会发生异常。  

 如果 Hive q 脚本中存在语法错误，或者如果 Hive 解释器在执行查询或脚本时遇到错误，则会发生此 Azure 机器学习错误。  

**解决方法：**

来自 Hive 的错误消息通常会在错误日志中报告回来，以便您可以基于特定错误执行操作。 

+ 打开模块并检查查询是否有错误。  
+ 通过登录到 Hadoop 群集的 Hive 控制台并运行查询，验证查询在 Azure 机器学习以外是否正常工作。  
+ 尝试在单独的行中将注释放置在 Hive 脚本中，而不是将可执行语句和注释混合到一个行中。  

### <a name="resources"></a>资源

请参阅以下文章，获取有关用于机器学习的 Hive 查询的帮助：

+ [创建 Hive 表并从 Azure Blob 存储加载数据](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [利用 Hive 查询浏览表中的数据](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [使用 Hive 查询在 Hadoop 群集中为数据创建功能](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [SQL 用户的 Hive 备忘单（PDF）](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|异常消息|
|------------------------|
|Hive 脚本不正确。|
|Hive 脚本 {0} 不正确。|


## <a name="error-0069"></a>错误 0069  
 如果指定的 SQL 脚本不正确，则会发生异常。  

 如果指定的 SQL 脚本具有语法问题，或者如果脚本中指定的列或表无效，则会出现 Azure 机器学习中的错误。 

 如果 SQL 引擎在执行查询或脚本时遇到任何错误，您将收到此错误。 通常会在错误日志中返回 SQL 错误消息，以便您可以基于特定错误执行操作。  

**解决方法：** 请重新访问该模块，检查 SQL 查询是否有错误。  

 通过直接登录到数据库服务器并运行查询来验证查询在 Azure ML 外是否正常工作。  

 如果有由模块异常报告的 SQL 生成的消息，请根据报告的错误采取措施。 例如，错误消息有时包含有关可能的错误的特定指导：
+ *不存在这样的列或缺少数据库*，这表示您可能键入了错误的列名称。 如果确定列名正确，请尝试使用括号或引号将列标识符括起来。
+ *Sql 逻辑错误 \<sql 关键字\>附近*，指示在指定关键字之前可能出现语法错误

  
|异常消息|
|------------------------|
|SQL 脚本不正确。|
|SQL 查询 "{0}" 不正确。|
|SQL 查询 "{0}" 不正确：{1}。|


## <a name="error-0070"></a>错误 0070  
 如果尝试访问的 Azure 表不存在，则会发生异常。  

 当你尝试访问不存在的 Azure 表时，会发生 Azure 机器学习中的错误。 如果在 Azure 存储中指定的表在从 Azure 表存储中进行读取或写入时不存在，则会收到此错误。 如果对所需的表的名称键入错误，或目标名称和存储类型不匹配，则可能会发生这种情况。 例如，你打算从表中读取，但改为输入 blob 的名称。  

**解决方法：** 请重新访问该模块，以验证表的名称是否正确。  

|异常消息|
|------------------------|
|Azure 表不存在。|
|Azure 表 "{0}" 不存在。|


## <a name="error-0072"></a>错误 0072  
 连接超时的情况下将发生异常。  

 当连接超时时，会发生 Azure 机器学习中的错误。如果当前数据源或目标存在连接问题（如 internet 连接缓慢），或者数据集很大，并且/或者要读取数据的 SQL 查询执行复杂的处理，则将收到此错误。  

**解决方法：** 确定当前是否存在与 Azure 存储或 internet 连接缓慢的问题。  

|异常消息|
|------------------------|
|发生连接超时。|


## <a name="error-0073"></a>错误 0073  
 如果在将列转换为其他类型时出现错误，则会发生异常。  

 如果无法将列转换为另一种类型，则会发生 Azure 机器学习中的错误。  如果模块需要特定类型，并且无法将该列转换为新类型，则将收到此错误。  

**解决方法：** 修改输入数据集，以便可以根据内部异常来转换列。  

|异常消息|
|------------------------|
|无法转换列。|
|无法将列转换为 {0}。|


## <a name="error-0075"></a>错误 0075  
如果在量化数据集时使用了无效的装箱函数，则会发生异常。  

当你尝试使用不受支持的方法或参数组合无效时，会发生 Azure 机器学习中的错误。  

**解决方法：**

此事件的错误处理是在早期版本的 Azure 机器学习中引入的，该版本允许对装箱方法进行更多的自定义。 当前所有的装箱方法都基于从下拉列表中进行的选择，因此，从技术上讲，不能再收到此错误。

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|异常消息|
|------------------------|
|使用无效的装箱函数。|


## <a name="error-0077"></a>错误 0077  
 如果传递了未知的 blob 文件写入模式，则会出现异常。  

 如果在 blob 文件目标或源的规范中传递了无效参数，则会出现此 Azure 机器学习错误。  

**解决方法：** 在几乎所有用于在 Azure blob 存储中导入或导出数据的模块中，通过使用下拉列表分配控制写入模式的参数值;因此，不可能传递无效值，因此不会出现此错误。 此错误将在更高版本中弃用。  

|异常消息|
|------------------------|
|不支持的 Blob 写入模式。|
|不支持的 blob 写入模式： {0}。|


## <a name="error-0078"></a>错误 0078  
 当[导入数据](import-data.md)的 HTTP 选项收到指示重定向的3xx 状态代码时，将发生异常。  

 如果[导入数据](import-data.md)的 HTTP 选项收到指示重定向的3xx （301、302、304等）状态代码，则会出现此 Azure 机器学习错误。 如果尝试连接到将浏览器重定向到其他页面的 HTTP 源，将会收到此错误。 出于安全原因，不允许重定向网站作为 Azure 机器学习的数据源。  

**解决方法：** 如果网站是受信任的网站，请直接输入重定向的 URL。  

|异常消息|
|------------------------|
|不允许 Http 重定向。|


## <a name="error-0079"></a>错误 0079  
 如果错误地指定了 Azure 存储容器名称，则会发生异常。  

 如果错误地指定了 Azure 存储容器名称，则会发生此错误 Azure 机器学习。 如果在写入 Azure Blob 存储时未同时指定容器和 blob （文件）的**路径（以 "容器开头"** 选项），则会收到此错误。  

**解决方法：** 重新访问[导出数据](export-data.md)模块，并验证 blob 的指定路径是否包含容器和文件名，格式为**容器/文件名**。  

|异常消息|
|------------------------|
|Azure 存储容器名称不正确。|
|Azure 存储容器名称 "{0}" 不正确;应为容器/blob 格式的容器名称。|


## <a name="error-0080"></a>错误 0080  
 如果模块不允许列缺少所有值，则会发生异常。  

 当模块使用的一个或多个列包含所有缺失值时，将生成 Azure 机器学习中的此错误。 例如，如果某个模块正在计算每个列的聚合统计信息，则它无法对不包含任何数据的列进行运算。 在这种情况下，模块执行会因此异常而停止。  

**解决方法：** 重新访问输入数据集并删除包含所有缺失值的列。  

|异常消息|
|------------------------|
|不允许包含所有缺失值的列。|
|列 {0} 包含所有缺少的值。|


## <a name="error-0081"></a>错误 0081  
 如果要减少的维数等于输入数据集中的功能列数，而且该数据集中至少包含一个稀疏功能列，则会在 PCA 模块中发生异常。  

 如果满足以下条件，则会生成 Azure 机器学习中的此错误：（a）输入数据集至少有一个稀疏列，（b）请求的最终维度数与输入维度的数目相同。  

**解决方法：** 请考虑将输出中的维数减少到小于输入中的维度数。 这在 PCA 的应用程序中很典型。   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|异常消息|
|------------------------|
|对于包含稀疏特征列的数据集，要减少到的维度数应小于特征列数。|


## <a name="error-0082"></a>错误 0082  
 如果模型不能成功地反序列化，则会发生异常。  

 当较新版本的 Azure 机器学习运行时因重大更改而无法加载已保存的机器学习模型或转换时，Azure 机器学习中会出现此错误。  

**解决方法：** 生成模型或转换的定型管道必须重新运行，模型或转换必须重新保存。  

|异常消息|
|------------------------|
|无法反序列化模型，因为它可能是使用早期序列化格式序列化的。 请重新训练并重新保存该模型。|


## <a name="error-0083"></a>错误 0083  
 如果用于训练的数据集不可用于具体类型的学习器，则会发生异常。  

 当数据集与正在训练的学习器不兼容时，将生成 Azure 机器学习中的此错误。 例如，数据集可能会在每一行中包含至少一个缺失值，因此在定型过程中将跳过整个数据集。 在其他情况下，某些机器学习算法（如异常检测）不希望标签出现，并且如果数据集中存在标签，则会引发此异常。  

**解决方法：** 请参阅用于检查输入数据集要求的学习器的文档。 检查列以查看是否存在所有必需的列。  

|异常消息|
|------------------------|
|用于训练的数据集无效。|
|{0} 包含用于定型的无效数据。|
|{0} 包含用于定型的无效数据。 学习器类型： {1}。|
|{0} 包含用于定型的无效数据。 学习器类型： {1}。 原因： {2}。|


## <a name="error-0084"></a>错误 0084  
 计算从 R 脚本生成的分数时，发生异常。 当前不支持该操作。  

 如果尝试使用其中一个模块来计算包含分数的 R 脚本输出的模型，则会出现 Azure 机器学习中的错误。  

**解决方法：**

|异常消息|
|------------------------|
|当前不支持对自定义模型生成的评分进行评估。|


## <a name="error-0085"></a>错误 0085  
 如果脚本计算失败且出现错误，则会发生异常。  

 当你运行的自定义脚本包含语法错误时，Azure 机器学习会发生此错误。  

**解决方法：** 在外部编辑器中查看代码，并检查是否有错误。  

|异常消息|
|------------------------|
|评估脚本期间出错。|
|脚本计算过程中出现以下错误，请查看输出日志以了解详细信息：----------从 {script_language} 解释器开始错误消息的开头----------{message}----------错误消息的结束时间为 {script_language} 解释器----------|


## <a name="error-0090"></a>错误0090  
 当 Hive 表创建失败时，将发生异常。  

 如果使用 "[导出数据](export-data.md)" 或其他选项将数据保存到 HDInsight 群集并且无法创建指定的 Hive 表，则会出现 Azure 机器学习中的错误。  

**解决方法：** 检查与群集关联的 Azure 存储帐户名称，并验证是否在模块属性中使用同一帐户。  

|异常消息|
|------------------------|
|无法创建 Hive 表。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称与通过 module 参数传递的名称相同。|
|无法创建 Hive 表 "{0}"。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称与通过 module 参数传递的名称相同。|
|无法创建 Hive 表 "{0}"。 对于 HDInsight 群集，请确保与群集关联的 Azure 存储帐户名称为 "{1}"。|


## <a name="error-0102"></a>错误 0102  
 无法提取 ZIP 文件时引发。  

 如果要导入 .zip 扩展名为 .zip 的压缩包，但该文件不是 zip 文件，或者该文件不使用支持的 zip 格式，则会出现 Azure 机器学习中的错误。  

**解决方法：** 请确保所选文件是有效的 .zip 文件，并且使用其中一种受支持的压缩算法对其进行压缩。  

 如果在以压缩格式导入数据集时遇到此错误，请验证所有包含的文件是否使用受支持的文件格式之一，并且是否为 Unicode 格式。  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 尝试将所需文件读取到新压缩的压缩文件夹，并再次尝试添加自定义模块。  

|异常消息|
|------------------------|
|给定的 ZIP 文件的格式不正确。|


## <a name="error-0105"></a>错误 0105  
 当模块定义文件包含不支持的参数类型时，将显示此错误。  
  
 当你创建自定义模块 xml 定义并且定义中的参数或参数的类型与支持的类型不匹配时，将生成 Azure 机器学习中的此错误。  
  
**解决方法：** 请确保自定义模块 xml 定义文件中任何**Arg**元素的 type 属性是受支持的类型。  
  
|异常消息|  
|------------------------|  
|不支持的参数类型。|  
|指定的参数类型 "{0}" 不受支持。|  


## <a name="error-0125"></a>错误 0125  
 当多个数据集的架构不匹配时引发。  

**解决方法：**

|异常消息|
|------------------------|
|数据集架构不匹配。|


## <a name="error-0127"></a>错误 0127  
 图像像素大小超过允许的限制  

 如果要从映像数据集中读取图像以进行分类，并且图像大于模型可以处理的数量，则会发生此错误。  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|异常消息|
|------------------------|
|图像像素大小超过允许的限制。|
|文件 "{0}" 中的图像像素大小超出了允许的限制： "{1}"。|


## <a name="error-0128"></a>错误0128  
 分类列的条件概率的数目超出了限制。  

**解决方法：**

|异常消息|
|------------------------|
|分类列的条件概率的数目超出了限制。|
|分类列的条件概率的数目超出了限制。 列 "{0}" 和 "{1}" 是有问题的对。|


## <a name="error-0129"></a>错误0129  
 数据集中的列数超出了允许的限制。  

**解决方法：**

|异常消息|
|------------------------|
|数据集中的列数超出了允许的限制。|
|"{Dataset_name}" 中数据集中的列数超出了允许值。|
|"{Dataset_name}" 中数据集中的列数超出了允许的 "{component_name}" 限制。|
|"{Dataset_name}" 中数据集中的列数超出了允许的 "{limit_columns_count}" 限制 "{component_name}"。|


## <a name="error-0134"></a>错误0134
当缺少标签列或标签行数不足时，将发生异常。  

当模块需要标签列，但你未在列选择中包含它，或者标签列缺少太多值时，将出现此错误。

如果上一个操作更改了数据集，从而使的行数不足以用于下游操作，也会出现此错误。 例如，假设您在**分区和示例**模块中使用表达式来按值划分数据集。 如果没有为表达式找到匹配项，则该分区生成的数据集之一将为空。

解决方法： 

 如果在列选择中包括标签列但无法识别，则使用 "[编辑元数据](edit-metadata.md)" 模块将其标记为标签列。

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->然后，可以使用 "[清理缺失数据](clean-missing-data.md)" 模块删除 "标签" 列中包含缺失值的行。 

 检查输入数据集以确保它们包含有效数据，并确保有足够的行来满足操作要求。 如果许多算法需要数据的最小行数，但数据只包含几行或仅包含一个标头，则会生成一条错误消息。

|异常消息|
|------------------------|
|当缺少标签列或标签行数不足时，将发生异常。|
|当缺少标签列或标记的行小于 {required_rows_count} 时，将发生异常。|
|当数据集 {dataset_name} 中的标签列缺失或包含的行数少于 {required_rows_count} 时，将发生异常。|


## <a name="error-0138"></a>错误0138  
 内存已用尽，无法完成模块的运行。 缩减数据集的缩减可能有助于缓解问题。  

 当运行的模块需要的内存超过 Azure 容器中的可用内存时，会发生此错误。 如果使用的是大型数据集，并且当前操作无法容纳在内存中，则可能会发生这种情况。  

**解决方法：** 如果尝试读取大型数据集，并且无法完成操作，则可能会对数据集进行缩减。  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|异常消息|
|------------------------|
|内存已用尽，无法完成模块的运行。|
|内存已用尽，无法完成模块的运行。 详细信息： {details}|


## <a name="error-0141"></a>错误0141  
 如果分类列和字符串列中选定的数值列数和唯一值太小，则会发生异常。  

 当所选列中的唯一值不足以执行此操作时，将发生 Azure 机器学习中的错误。  

**解决方法：** 某些操作对功能列和分类列执行统计操作，如果没有足够的值，则操作可能失败或返回无效结果。 检查数据集以查看功能和标签列中有多少个值，并确定尝试执行的操作的统计是否有效。  

 如果源数据集有效，你还可以检查某些上游数据操作或元数据操作是否已更改数据并删除某些值。  

 如果上游操作包括拆分、采样或重新采样，请验证输出中是否包含预期的行数和值数。  

|异常消息|
|------------------------|
|所选数值列的数目和分类和字符串列中的唯一值太小。|
|分类列和字符串列（当前 {0}）中所选数值列和唯一值的总数至少应为 {1}。|


## <a name="error-0154"></a>错误0154  
 当用户尝试联接列类型不兼容的键列上的数据时，将发生异常。

|异常消息|
|------------------------|
|键列元素类型不兼容。|
|键列元素类型不兼容。（左： {keys_left}; right： {keys_right}）|


## <a name="error-0155"></a>错误0155  
 当 dataset 的列名不是字符串时，将发生异常。

|异常消息|
|------------------------|
|列名不是字符串。|
|列名： {column_names} 不是字符串。|


## <a name="error-0156"></a>错误0156  
 当未能从 Azure SQL 数据库读取数据时，将发生异常。

|异常消息|
|------------------------|
|未能从 Azure SQL 数据库读取数据。|
|未能从 Azure SQL 数据库读取数据： {detailed_message} 数据库： {database_server_name}： {database_name} 查询： {sql_statement}|


## <a name="error-0157"></a>错误0157  
 找不到数据存储。

|异常消息|
|------------------------|
|数据存储信息无效。|
|数据存储信息无效。 未能获取工作区 "{workspace_name}" 中的 AzureML 数据存储 "{datastore_name}"。|


## <a name="error-1000"></a>错误1000  
内部库异常。  

提供此错误是为了捕获未处理的内部引擎错误。 因此，导致此错误的原因可能会不同，具体取决于生成错误的模块。  

若要获得更多帮助，建议你将错误附带的详细消息发布到 Azure 机器学习论坛，同时提供方案说明（包括用作输入的数据）。 此反馈将帮助我们确定错误的优先级，并确定最重要的问题，以便进行进一步的工作。  

|异常消息|
|------------------------|
|库异常。|
|库例外： {0}。|
|未知库异常： {0}。 {1}。|

