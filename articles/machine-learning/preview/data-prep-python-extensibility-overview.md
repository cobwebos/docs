---
title: "将 Python 扩展性与 Azure 机器学习数据准备结合使用 | Microsoft Docs"
description: "本文提供有关如何使用 Python 代码来扩展数据准备功能的概述和一些详细示例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4b888facdba2eb5ff48bcbf43c93c1b75183cbad
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="data-preparations-python-extensions"></a>数据准备 Python 扩展
作为一种填补内置功能之间功能差距的方法，Azure 机器学习数据准备包含多个级别的扩展性。 在本文档中，我们将通过 Python 脚本概述扩展性。 

## <a name="custom-code-steps"></a>自定义代码步骤 
数据准备中包括下列自定义步骤，用户可以在其中编写代码：

* 文件读取器*
* 写入器*
* 添加列
* 高级筛选器
* 转换数据流
* 转换分区

*这些步骤当前在 Spark 执行过程中不受支持。

## <a name="code-block-types"></a>代码块类型 
对于每个步骤，我们支持两个代码块类型。 首先，我们支持以原样执行的空 Python 表达式。 其次，我们支持 Python 模块，其中我们在你提供的代码中调用具有已知签名的特定函数。

例如，可以添加一个新列，根据以下两种方式计算另一列的日志：

表达式 

```python    
    math.log(row["Score"])
```

模块 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


模块模式中的添加列转换期望找到名为 `newvalue` 的函数，该函数接受行变量并返回列的值。 此模块可以包含任意数量的 Python 代码以及其他函数、导入等等。

下列部分中讨论了每个扩展点的详细信息。 

## <a name="imports"></a>导入 
如果使用“表达式”块类型，则仍可向代码中添加 **import** 语句。 它们必须在代码的顶行中进行分组。

正确 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

错误  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
如果使用“模块”块类型，则可以按照所有常规的 Python 规则来使用 **import** 语句。 

## <a name="default-imports"></a>默认导入
以下导入始终包含在代码中，且始终可用。 不需要重新导入它们。 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>安装新包
若要使用默认情况下未安装的包，首先需要将其安装到数据准备使用的环境中。 此安装既需要在本地计算机上完成，也需要在你希望运行的任何计算目标上完成。

若要在计算目标中安装包，必须修改位于项目根目录下的 aml_config 文件夹中的 conda_dependencies.yml 文件。

### <a name="windows"></a>Windows 
在 Windows 中找到位置的方法是，查找 python 特定于应用程序的安装及其脚本目录。 默认位置为：  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

然后运行以下命令之一： 

`conda install <libraryname>` 

或 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
在 Mac 中找到位置的方法是，查找 Python 特定于应用程序的安装及其脚本目录。 默认位置为： 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

然后运行以下命令之一： 

`./conda install <libraryname>`

或 

`./pip install <libraryname>`

## <a name="column-data"></a>列数据 
可从使用点表示法的行或通过使用键-值表示法来访问列数据。 不能使用点表示法访问包含空格或特殊字符的列名称。 `row` 变量应始终在 Python 扩展的两种模式中进行定义（模块和表达式）。 

示例 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>文件读取器 
### <a name="purpose"></a>目的 
“文件读取器”扩展点允许完全控制将文件读取到数据流的过程。 系统调用代码，在应处理的文件列表中传递。 代码需要创建并返回一个 Pandas 数据帧。 

>[!NOTE]
>此扩展点在 Spark 中无效。 


### <a name="how-to-use"></a>如何使用 
从“打开数据源”向导访问此扩展点。 选择第一页上的“文件”，然后选择文件位置。 在“选择文件参数”页上的“文件类型”下拉列表中，选择“自定义文件(脚本)”。 

将为代码提供名为“df”的 Pandas 数据帧，其中包含需要读取的文件信息。 如果选择打开包含多个文件的目录，则数据帧包含多个行。  

此数据帧具有以下列：

- 路径：要读取的文件。
- PathHint：指示该文件的所在位置。 值：Local、AzureBlobStorage 和 AzureDataLakeStorage。
- AuthenticationType：用于访问该文件的身份验证类型。 值：None、SasToken 和 OAuthToken。
- AuthenticationValue：包含 None 或要使用的令牌。

### <a name="syntax"></a>语法 
表达式 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


模块  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>写入器 
### <a name="purpose"></a>目的 
此“写入器”扩展点允许完全控制从数据流写入数据的过程。 系统调用代码，在数据帧中传递。 代码可以使用数据帧按照需要写入数据。 

>[!NOTE]
>“写入器”扩展点在 Spark 中无效。


### <a name="how-to-use"></a>如何使用 
可以使用“写入数据流(脚本)”块来添加此扩展点。 顶级“转换”菜单中已提供此块。

### <a name="syntax"></a>语法 
表达式

```python
    df.to_csv('c:\\temp\\output.csv')
```

模块

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
此自定义写入块可以存在于一个步骤列表中间。 如果使用模块，则写入函数必须返回作为后续步骤的输入的数据帧。 

## <a name="add-column"></a>添加列 
### <a name="purpose"></a>目的
“添加列”扩展点允许编写 Python 来计算新的列。 你编写的代码有权访问完整行。 它必须为每个行返回新的列值。 

### <a name="how-to-use"></a>如何使用
可以使用“添加列(脚本)”块来添加此扩展点。 它在顶级“转换”菜单以及列上下文菜单中可用。 

### <a name="syntax"></a>语法
表达式

```python
    math.log(row["Score"])
```

模块 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>高级筛选器
### <a name="purpose"></a>目的 
“高级筛选器”扩展点允许编写自定义筛选器。 你有权访问整个行，并且代码必须返回 True（包括行）或 False（排除行）。 

### <a name="how-to-use"></a>如何使用
可以使用“高级筛选器(脚本)”块来添加此扩展点。 顶级“转换”菜单中已提供此块。 

### <a name="syntax"></a>语法

表达式

```python
    row["Score"] > 95
```

模块  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>转换数据流
### <a name="purpose"></a>目的 
“转换数据流”扩展点允许完全转换数据流。 你有权访问包含所有正在处理的列和行的 Pandas 数据帧。 代码必须返回使用新数据的 Pandas 数据帧。 

>[!NOTE]
>在 Python 中，如果使用此扩展，则所有数据都要加载到 Pandas 数据帧中的内存。 
>
>在 Spark 中，所有数据都会被收集到单个工作节点。 如果数据非常大，可能导致辅助角色耗尽内存。 请谨慎使用。

### <a name="how-to-use"></a>如何使用 
可以使用“转换数据流(脚本)”块来添加此扩展点。 顶级“转换”菜单中已提供此块。 
### <a name="syntax"></a>语法 

表达式

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

模块 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>转换分区  
### <a name="purpose"></a>目的 
“转换分区”扩展点允许转换数据流中的一个分区。 你有权访问包含该分区所有列和行的 Pandas 数据帧。 代码必须返回使用新数据的 Pandas 数据帧。 

>[!NOTE]
>在 Python 中，根据数据的大小，可能最终会得到一个单独的分区或多个分区。 在 Spark 中，使用的是包含给定工作节点上某个分区数据的数据帧。 在这两种情况下，不能假定你有权访问整个数据集。 


### <a name="how-to-use"></a>如何使用
可以使用“转换分区(脚本)”块来添加此扩展点。 顶级“转换”菜单中已提供此块。 

### <a name="syntax"></a>语法 

表达式 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

模块 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>错误值  
数据准备中存在错误值的概念。 

可能会在自定义 Python 代码中遇到错误值。 它们是调用 `DataPrepError` 的 Python 类的实例。 此类封装 Python 异常，并有几种属性。 这些属性包含有关处理原始值时发生的错误的信息，以及有关原始值的信息。 


### <a name="datapreperror-class-definition"></a>DataPrepError 类定义
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
在数据准备 Python 框架中创建 DataPrepError 通常如下所示： 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>如何使用 
Python 可能会使用前面的创建方法，在一个扩展点运行来生成 DataPrepErrors 作为返回值。 在扩展点处理数据时更有可能遇到 DataPrepErrors。 在这种情况下，自定义 Python 代码需要处理 DataPrepError 作为有效的数据类型。

#### <a name="syntax"></a>语法 
表达式 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
模块 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
