---
title: 执行 R 脚本：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“执行 R 脚本”模块来运行 R 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: d5ef8d6a9b0c0039b500ce9d0238609e8a8edc93
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908014"
---
# <a name="execute-r-script-module"></a>“执行 R 脚本”模块

本文介绍如何使用 "执行 R 脚本" 模块在 Azure 机器学习设计器管道中运行 R 代码。

使用 R，可以执行现有模块当前不支持的任务，例如： 
- 创建自定义数据转换
- 使用你自己的指标来评估预测
- 使用未在设计器中作为独立模块实施的算法来生成模型

## <a name="r-version-support"></a>R 版本支持

Azure 机器学习设计器使用 R 的 CRAN（综合 R 存档网络）分发。当前使用的版本为 CRAN 3.5.1。

## <a name="supported-r-packages"></a>支持的 R 包

R 环境预装有 100 多个包。 有关完整列表，请参阅[预安装的 R 包](#preinstalled-r-packages)部分。

也可以将以下代码添加到任意“执行 R 脚本”模块来查看已安装的包。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> 如果管道包含的多个“执行 R 脚本”模块需要预安装列表中未包含的包，请在每个模块中安装这些包。 

## <a name="installing-r-packages"></a>安装 R 程序包
若要安装其他 R 包，请使用 `install.packages()` 方法。 包是针对每一个“执行 R 脚本”模块分别安装的。 它们不在其他“执行 R 脚本”模块之间共享。

> [!NOTE]
> 在安装包时，请指定 CRAN 存储库，例如 `install.packages("zoo",repos = "http://cran.us.r-project.org")`。

此示例演示如何安装 Zoo：
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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
 > [!NOTE]
 > 安装包之前，请检查它是否已经存在，以避免重复安装。 重复安装可能会导致 Web 服务请求超时。     

## <a name="uploading-files"></a>上传文件
“执行 R 脚本”模块支持通过使用 Azure 机器学习 R SDK 上传文件。

以下示例演示了如何在“执行 R 脚本”中上传图像文件：
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

管道运行完成后，可在模块的右侧面板中预览该图像。

> [!div class="mx-imgBorder"]
> ![预览上传的图像](media/module/upload-image-in-r-script.png)

## <a name="access-to-registered-dataset"></a>访问已注册的数据集

可以参阅以下示例代码，在工作区中[访问已注册的数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets#access-datasets-in-your-script)：

```R
        azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>如何配置“执行 R 脚本”

“执行 R 脚本”模块包含可用作起点的代码示例。 若要配置“执行 R 脚本”模块，请提供一组输入和要运行的代码。

![R 模块的输入示意图](media/module/execute-r-script.png)

使用此模块加载时，存储在设计器中的数据集将自动转换为 R 数据帧。

1.  将“执行 R 脚本”模块添加到管道。  

1. 连接该脚本需要的任何输入。 输入是可选的，可以包含数据和其他 R 代码。

    * **Dataset1**：引用第一个输入作为 `dataframe1`。 输入数据集必须是 CSV、TSV 或 ARFF 格式的文件。 或者可以连接 Azure 机器学习数据集。

    * **Dataset2**：引用第二个输入作为 `dataframe2`。 此数据集也必须是 CSV、TSV、ARFF 格式的文件，或者是 Azure 机器学习数据集。

    * **脚本包**：第三个输入接受 .zip 文件。 压缩文件可以包含多个文件和多种文件类型。

1. 在“R 脚本”文本框中，键入或粘贴有效的 R 脚本。

    > [!NOTE]
    > 编写脚本时请小心谨慎。 确保没有语法错误，例如，使用未声明的变量或未导入的模块或函数。 请特别注意本文结尾处的预安装包列表。 若要使用未列出的包，请通过脚本安装它们。 例如 `install.packages("zoo",repos = "http://cran.us.r-project.org")`。
    
    为了帮助你入门，“R 脚本”文本框中预填充了可编辑或替换的代码示例。
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    入口点函数必须有输入参数 `Param<dataframe1>` 和 `Param<dataframe2>`，即使该函数中没有使用这些参数。

    > [!NOTE]
    > 传递到“执行 R 脚本”模块的数据会被引用为 `dataframe1` 和 `dataframe2`，这不同于 Azure 机器学习设计器（设计器引用为 `dataset1`、`dataset2`）。 请确保脚本中正确引用了输入数据。  
 
    > [!NOTE]
    > 现有 R 代码可能需要稍做更改才能在设计器管道中运行。 例如，以 CSV 格式提供的输入数据应显式转换为数据集，然后才能在代码中使用。 R 语言中使用的数据和列类型与在设计器中使用的数据和列类型在某些方面也有所不同。

    如果脚本大于 16KB，请使用脚本包端口以避免错误，如命令行超过 16597 个字符的限制。 
    
    将脚本和其他自定义资源捆绑到一个 zip 文件，然后将该 zip 文件作为文件数据集上传到工作室。 然后可以从设计器创作页面左侧模块窗格中的“我的数据集”列表中拖取数据集模块。 将数据集模块连接到“执行 R 脚本”模块的“脚本包”端口。
    
    下面是使用脚本包中的脚本的示例代码：

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  对于“随机种子”，请输入要在 R 环境中用作随机种子值的值。 此参数相当于在 R 代码中调用 `set.seed(value)`。  

1. 提交管道。  

## <a name="results"></a>结果

“执行 R 脚本”模块可返回多个输出，但必须将它们作为 R 数据帧提供。 数据帧会自动转换为设计器中的数据集，以便与其他模块兼容。

来自 R 的标准消息和错误将返回到模块的日志中。

如果需要通过 R 脚本输出结果，可在该模块右侧面板中的“输出 + 日志”选项卡下的“70_driver_log”中查找输出的结果。

## <a name="sample-scripts"></a>示例脚本

通过使用自定义 R 脚本来扩展管道的方法有多种。 本部分提供常见任务的示例代码。


### <a name="add-an-r-script-as-an-input"></a>添加 R 脚本作为输入

“执行 R 脚本”模块支持任意 R 脚本文件作为输入。 要使用这些文件，必须将它们作为 .zip 文件的一部分上传到工作区。

1. 若要将包含 R 代码的 .zip 文件上传到工作区，请转到“数据集”资产页。 选择“创建数据集”，然后选择“从本地文件”和“文件”数据集类型选项  。  

1. 验证左侧模块树中“数据集”类别下“我的数据集”列表中是否存在该压缩文件 。

1.  将数据集连接到“脚本包”输入端口。

1. 该 .zip 文件中的所有文件在管道运行时都是可用的。 

    如果脚本包文件中已包含目录结构，则会保留结构。 但是，必须更改代码，以将目录“./Script Bundle”追加到路径前面。

### <a name="process-data"></a>处理数据

以下示例演示如何缩放和规范化输入数据：

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>读取 .zip 文件作为输入

此示例演示如何使用 .zip 文件中的数据集作为“执行 R 脚本”模块的输入。

1. 创建 CSV 格式的数据文件，并将其命名为“mydatafile.csv”。
1. 创建一个 .zip 文件，并将该 CSV 文件添加到此存档。
1. 将压缩文件上载到 Azure 机器学习工作区。 
1. 将结果数据集连接到“执行 R 脚本”模块的“ScriptBundle”输入 。
1. 使用以下代码从压缩文件中读取 CSV 数据。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>复制行

此示例演示如何复制数据集中的正面记录来平衡示例：

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>在“执行 R 脚本”模块之间传递 R 对象

可通过使用内部序列化机制在“执行 R 脚本”模块的实例之间传递 R 对象。 此示例假设需要在两个“执行 R 脚本”模块之间移动名为 `A` 的 R 对象。

1. 将第一个“执行 R 脚本”模块添加到管道。 然后在“R 脚本”文本框中输入以下代码，以创建序列化对象 `A` 作为模块输出数据表中的列：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    此时会完成显式转换到整数类型，因为序列化函数输出 R `Raw` 格式的数据，而设计器不支持该格式。

1. 添加“执行 R 脚本”模块的第二个实例，并将其连接到以前模块的输出端口。

1. 在 " **R 脚本** " 文本框中键入以下代码，以 `A` 从输入数据表提取对象。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>预安装的 R 包

以下预安装的 R 包当前可用：

| 程序包      | 版本    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| 启动         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
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
| foreign      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
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
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
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
| recipes      | 0.1.5      | 
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
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
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
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 动物园          | 1.8-6      | 

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
