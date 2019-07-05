---
title: 执行 R 脚本：模块参考
titleSuffix: Azure Machine Learning service
description: 了解如何使用执行 R 脚本模块在 Azure 机器学习服务中运行 R 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518048"
---
# <a name="execute-r-script"></a>执行 R 脚本

本文介绍如何使用**执行 R 脚本**模块在可视界面试验中运行 R 代码。

使用 R，您可以执行目前不支持现有模块如的任务： 
- 创建自定义数据转换
- 使用你自己的指标来评估预测
- 作为独立模块可视界面中使用未实施的算法生成模型

## <a name="r-version-support"></a>R 版本支持

Azure 机器学习服务的可视界面使用 CRAN (Comprehensive R Archive Network) 分发版的。当前所用的版本是 CRAN 3.5.1。

## <a name="supported-r-packages"></a>受支持的 R 包

预安装了 100 多个包的 R 环境。 有关完整列表，请参阅部分[预安装 R 包](#pre-installed-r-packages)。

此外可以将以下代码添加到任何**执行 R 脚本**模块，并查看已安装的包。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>安装 R 程序包
若要安装其他 R 包，请使用`install.packages()`方法。 请务必指定 CRAN 存储库。 每个安装包**执行 R 脚本**模块，并在其他之间不共享**执行 R 脚本**模块。

此示例演示如何安装 Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>如何配置执行 R 脚本

**执行 R 脚本**模块包含示例代码，可以使用作为起始点。 若要配置**执行 R 脚本**模块，提供一组的输入和要执行的代码。

![R 模块](media/module/execute-r-script.png)

数据集存储在可视界面会自动转换为 R 数据帧使用此模块加载时。

1.  添加**执行 R 脚本**模块在试验。

    > [!NOTE]
    > 所有数据都传递给**执行 R 脚本**模块转换为 R`data.frame`格式。

1. 连接所需脚本的任何输入。 输入是可选的可以包括数据和其他 R 代码。

    * **Dataset1**:引用作为第一个输入`dataframe1`。 输入数据集的格式必须为 CSV、 TSV、 ARFF 或可以连接的 Azure 机器学习数据集。

    * **Dataset2**:引用作为第二个输入`dataframe2`。 此数据集还必须格式化为 CSV、 TSV、 ARFF 文件，或作为 Azure 机器学习数据集。

    * **脚本绑定**:第三个输入接受 ZIP 文件。 压缩后的文件可以包含多个文件和多个文件类型。

1. 在中**R 脚本**文本框中，键入或粘贴有效的 R 脚本。

    若要帮助你开始， **R 脚本**文本框中已经填充了示例代码中，你可以编辑或替换。

    * 该脚本必须包含一个名为函数`azureml_main`，这是此模块的入口点。

    * 入口点函数可以包含最多两个输入的参数：`Param<dataframe1>`和 `Param<dataframe2>`
 
    > [!NOTE]
    >  现有 R 代码可能需要少量更改以可视界面试验中运行。 例如，以 CSV 格式提供的输入的数据应显式转换为数据集才能在代码中使用。 R 语言中使用的数据和列类型在某些方面与可视界面中使用的数据和列类型也存在差异。

1.  **随机种子**:键入要在 R 环境内用作随机种子值的值。 此参数等效于调用`set.seed(value)`R 代码中。  

1. 运行试验。  

## <a name="results"></a>结果

**执行 R 脚本**模块可以返回多个输出，但它们必须提供为 R 数据帧。 数据帧会自动转换为与其他模块的兼容性的直观界面的数据集。

标准消息和错误从 R 返回给模块的日志。

## <a name="sample-scripts"></a>示例脚本

有很多种，可以使用自定义 R 脚本来扩展试验。  本部分提供用于常见任务的示例代码。


### <a name="add-r-script-as-an-input"></a>作为输入添加 R 脚本

**执行 R 脚本**模块支持任意 R 脚本文件作为输入。 若要执行此操作，它们必须上载到你的工作区作为 ZIP 文件的一部分。

1. 若要上传包含到你的工作区的 R 代码的 ZIP 文件，请单击**新建**，单击**数据集**，然后选择**从本地文件**和**Zip 文件**选项。  

1. 验证压缩的文件现已推出**保存的数据集**列表。

1.  连接到数据集**脚本捆绑包**输入端口。

1. 在运行时的试验过程都可以 ZIP 文件中包含的所有文件。 

    如果脚本捆绑包文件包含的目录结构，将保留该结构。 但是，必须更改你的代码来添加目录 **。 脚本捆绑 /** 到路径。

### <a name="process-data"></a>处理数据

下面的示例演示如何缩放和规范化输入的数据：

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>读取 ZIP 文件作为输入

此示例演示如何使用 ZIP 文件中的数据集作为输入**执行 R 脚本**模块。

1. 创建数据文件以 CSV 格式，并将其命名为"mydatafile.csv"。
1. 创建一个 ZIP 文件并将 CSV 文件添加到存档。
1. 将压缩后的文件上传到 Azure 机器学习工作区。 
1. 连接到生成的数据集**ScriptBundle**的输入你**执行 R 脚本**模块。
1. 使用以下代码从压缩的文件中读取 CSV 数据。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>复制行

此示例演示如何进行复制的数据集来平衡样本中的正记录：

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>执行 R 脚本模块之间传递 R 对象

您可以传递 R 对象的实例之间**执行 R 脚本**模块通过使用内部序列化机制。 此示例假定你想要将名为 R 对象移动`A`两个之间**执行 R 脚本**模块。

1. 添加第一个**执行 R 脚本**到自己的实验，并键入下面的代码中的模块**R 脚本**文本框中，用于创建序列化的对象`A`作为数据表输出模块中的列：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    到整数类型的显式转换做是因为序列化函数输出的数据`Raw`格式，这不可视界面的支持。

1. 添加的第二个实例**执行 R 脚本**模块，并将其连接到前一模块的输出端口。

1. 键入下面的代码**R 脚本**文本框中提取对象`A`从输入数据表。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>预安装的 R 包

可使用预安装的 R 包的当前列表：

|              |            | 
|--------------|------------| 
| package      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| 基本         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| 启动         | 1.3-22     | 
| 扫把        | 0.5.2      | 
| callr        | 3.2.0      | 
| 插入符号        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| 色彩空间   | 1.4-1      | 
| 编译器     | 3.5.1      | 
| 笔       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| 评估     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| 外      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 泛型     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| 粘附         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| 图形     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| 网格         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| 迭代器    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| 添加标签     | 0.3        | 
| 点阵      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 第          | 
| MASS         | 7.3-51.4   | 
| 矩阵       | 1.2-17     | 
| 方法      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| 并行     | 3.5.1      | 
| 支柱       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| 进度     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| 食谱      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| 缩放       | 1.0.0      | 
| 选择      | 0.4-1      | 
| 空间      | 7.3-11     | 
| 自由绘制曲线      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| 生存     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| 工具        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| Utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| 须线      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 动物园          | 1.8-6      | 

## <a name="next-steps"></a>后续步骤

请参阅[可用的模块集](module-reference.md)到 Azure 机器学习服务。 