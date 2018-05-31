---
title: 在 Azure 机器学习中创作自定义 R 模块 | Microsoft Docs
description: 在 Azure 机器学习中创作自定义 R 模块快速入门。
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 945c238411dac8f2e64666935308ef3c1cb2f0ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30186329"
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>在 Azure 机器学习中创作自定义 R 模块
本主题介绍如何在 Azure 机器学习中创建和部署自定义 R 模块。 本主题解释什么是自定义 R 模块以及要使用什么文件来定义该模块。 本主题举例说明如何构建定义模块的文件以及如何在机器学习工作区中注册用于部署的模块。 随后本主题详细描述了定义自定义模块所使用的元素和属性。 本主题还讨论了如何使用辅助功能和文件以及多个输出。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>什么是自定义 R 模块？
**自定义模块**是一个用户定义的模块，可将其上传至工作区并作为 Azure 机器学习的一部分运行。 **自定义模块**是一个可执行用户定义的 R 函数的自定义模块。 **R** 是一种用于统计计算和图形的编程语言，统计学家和科学奖将其广泛用于运行一些算法。 目前，R 是自定义模块唯一支持的语言，但在未来发布的版本中会计划支持其他语言。

自定义模型在 Azure 机器学习中处于**优先地位**，因为某种意义上来说可以像使用其他模块一样使用自定义模块。 它们可与其他模块一起运行，可包含在已发布实验或可视化中。 可控制模块实施的算法、使用的输入和输出端口、建模参数以及其他各种运行时操作。 包含自定义模块的实验也可发布到 Azure AI 库方便共享。

## <a name="files-in-a-custom-r-module"></a>自定义 R 模块中的文件
自定义 R 模块由 .zip 文件定义，该文件至少包含两个文件：

* 一个可实施模块公开的 R 函数的**源文件**
* 一个描述自定义模块接口的 **XML 定义文件**

也可在 .zip 文件中包含提供功能的辅助文件，可通过自定义模块访问此功能。 此选项在快速入门示例下方的 **XML 定义文件中的元素**参考部分的**参数**部分有所描述。

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>快速入门示例：定义、包和注册自定义模块
此示例阐述如何构建自定义 R 模块所需的文件，如何将文件打包到一个 zip 文件，然后在机器学习工作区中注册该模块。 可从[下载 CustomAddRows.zip 文件](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409)下载示例 zip 包和示例文件。

## <a name="the-source-file"></a>源文件
以**自定义添加行**模块为例，该模块可修改用于从两个数据集（数据帧）连接行（观察值）的**添加行**模块的标准实施。 标准**添加行**模块会使用 `rbind` 算法将第二个输入数据集的行附加到第一个输入数据集的末尾。 同样自定义 `CustomAddRows` 函数会接受两个数据集，但它还会接受布尔交换参数作为一个额外的输入。 如果交换参数设置为 **FALSE**，函数会返回与标准实现相同的数据集。 但如果交换参数为 **TRUE**，则函数会将第一个输入数据集的行改为附加到第二个数据集的末尾。 CustomAddRows.R 文件包含**自定义添加行**模块公开的 R `CustomAddRows` 函数的实施，该文件拥有如下 R 代码。

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>XML 定义文件
要将此 `CustomAddRows` 函数公开，作为 Azure 机器学习模块，必须创建 XML 定义文件来指定**自定义添加行**模块的外观和行为。 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


但要注意，XML 文件中**输入**和**参数**元素的**ID**属性的值必须与 CustomAddRows.R 文件中 R 代码的函数参数名正确匹配：（示例中的*数据集1*、*数据集2*和*切换*）。 同样，**语言**元素的 **entryPoint** 属性必须与 R 脚本中的函数名正确匹配：（示例中的 *CustomAddRows*）。 

与此相反，**输出**元素 **ID** 属性与 R 脚本中的所有变量都不相符。 需要多个输出时，只需从 R 函数返回一个列表，列表中的结果需与 XML 文件中公开的**输出**元素*顺序相同*。

### <a name="package-and-register-the-module"></a>包和注册模块
将两个文件另存为 CustomAddRows.R 和 CustomAddRows.xml，然后将两个文件一起压缩为 CustomAddRows.zip 文件。

要在机器学习工作区中进行注册，请转到机器学习工作室中的工作区，单击底部的“+新建”按钮，并选择“模块”->“从 ZIP 包”，上传新的**自定义添加行**模块。

![上传 Zip](./media/custom-r-modules/upload-from-zip-package.png)

现可准备通过机器学习实验访问**自定义添加行** 模块。

## <a name="elements-in-the-xml-definition-file"></a>XML 定义文件中的元素
### <a name="module-elements"></a>模块元素
**模块**元素用于在 XML 文件中定义自定义模块。 可使用多个**模块**元素在一个 XML 文件中定义多个模块。 工作区中的每个模块必须具有唯一的名称。 使用与现有自定义模块相同的名称注册自定义模块，新的模块会替代现有模块。 但自定义模块可使用与现有 Azure 机器学习模块相同的名称进行注册。 此时模块会出现在模块面板的“自定义”类别中。

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


可在**模块**元素中指定其他两个可选元素：

* 嵌入模块的**所有者**元素  
* **描述**元素，该元素包含在模块快速帮助上显示的文本，将鼠标悬停在机器学习 UI 中时，会出现该文本。

模块元素中字符限制规则：

* **模块**元素中**名称**属性的值长度不能超过 64 个字符。 
* **描述**元素的内容长度不能超过 128 个字符。
* **所有者**元素的内容长度不能超过 32 个字符。

模块结果可以是确定或不确定的。\*\*默认情况下，所有模块都被视为是确定的。 也就是说，给定一组不变的输入参数和数据，模块应返回相同的结果 eacRAND 或返回其运行的时间。 考虑到此种情况，如果参数或输入数据发生改变，Azure 机器学习工作室只会重新运行标记为确定的模块。 通过返回已缓存的结果也可大幅提高实验执行速度。

存在不确定的函数，例如 RAND 或返回当前日期或时间的函数。 如果模块使用不确定的函数，则可将可选 **isDeterministic** 属性设置为 **FALSE**，将模块指定为不确定。 这可确保无论何时运行实验，都可重新运行模块，即使模块输入和参数发生了改变。 

### <a name="language-definition"></a>语言定义
XML 定义文件中的**语言**元素用于指定自定义模块的语言。 目前，R 是唯一受支持的语言。 **sourceFile** 属性的值必须是 R 的文件名，文件包含运行模块时要调用的函数。 此文件必须是 zip 包的一部分。 **entryPoint** 属性的值是被调用的函数名，且必须与源文件中定义的一个有效函数相匹配。

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>端口
自定义模块的输入和输出端口是在 XML 定义文件**端口**部分的子元素中进行定义的。 这些元素的顺序决定的用户体验的布局 (UX)。 XML 文件**端口**元素中列出的第一个子**输入**或**输出**会成为机器学习 UX 最左边的输入端口。
每个输入和输出端口可能有一个可选**说明**子元素，用于指定鼠标悬停在机器学习 UI 中的端口时显示的文本。

**端口规则**：

* 最大**输入和输出端口**数为每个 8 个端口。

### <a name="input-elements"></a>输入元素
通过输入端口可向 R 函数和工作区传送数据。 输入端口支持的**数据类型**如下： 

**DataTable：** 此类数据会作为 data.frame 传送到 R 函数。 事实上，所有机器学习支持的类型（例如，CSV 文件或 ARFF 文件）和所有与 **DataTable** 兼容的类型都将自定转换为 data.frame。 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

与每个 **DataTable** 输入端口相关联的**ID** 属性必须拥有一个唯一的值且此值必须与 R 函数中其相应的命名参数相匹配。
未作为输入传送到实验的可选 **DataTable** 端口拥有传送到 R 函数的 **NULL** 值，且如果未连接输入，则会忽略可选 zip 端口。 **DataTable** 和 **ZIP** 类型均可选择 **isOptional** 属性，属性默认为 FALSE。

**Zip：** 自定义模块可接受 zip 文件作为输入。 此输入会解压缩到函数的 R 工作目录中

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

对于自定义 R 模块，Zip 端口的 ID 无需与 R 函数的任何参数匹配。 这是因为自动提取 zip 文件到 R 工作目录。

**输入的规则：**

* **输入**元素的 **ID** 属性值必须为有效的 R 变量名称。
* **输入**元素的 **ID** 属性值不可超过 64 个字符。
* **输入**元素的 **name** 属性值不可超过 64 个字符。
* **描述**元素的内容不能超过 128 个字符
* **输入**元素的 **type** 属性值必须是 Zip 或 DataTable。
* 不需要**输入**元素的 **isOptional** 属性（且未指定时，默认为 false ）；但如果指定，则属性必须为 true 或 false。

### <a name="output-elements"></a>输出元素
**标准输出端口：** 输出端口被映射到 R 函数返回的值上，可用于后续模块。 DataTable 是当前支持的唯一标准输出端口类型。 （随后会支持 Learners 和 Transforms。）按此定义的 *DataTable* 输出：

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

对于自定义 R 模块中的输入而言，**ID** 属性值无需与 R 脚本相对应，但该值必须唯一。 对于单个模块输出，R 函数返回的值必须为 *data.frame*。 若要输出多个受支持数据类型的对象，需在 XML 定义文件中指定合适的端口且对象必须作为列表返回。 从左至右分配输出对象到输出端口，这反应出了返回列表中对象的排列顺序。

例如，如果想要修改**自定义添加列**模块，输出原始的两个数据集，数据集1 和数据集2，以及新联接的数据集，数据集（排列顺序从左至右依次是：数据集，数据集1，数据集2），请按如下所示在 CustomAddRows.xml 文件中定义输出端口：

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


‘CustomAddRows.R’中顺序正确的对象返回列表：

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**可视化输出：** 还可以指定*可视化*类型的输出端口，端口可显示 R 图形设备输出和控制台输出。 此端口不包含在 R 函数输出中且不会干扰其他输出端口类型的顺序。 若要添加可视化端口到自定义模块，向其 **type** 属性添加一个带有可视化值的**输出**元素：

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**输出规则：**

* **输出**元素的 **ID** 属性值必须为有效的 R 变量名称。
* **输出**元素的 **ID** 属性值不可超过 32 个字符。
* **输出**元素的 **name** 属性值不可超过 64个字符。
* **输出**元素的 **type** 属性值必须是 可视化。

### <a name="arguments"></a>参数
通过在**参数**元素中定义的模块参数可向 R 函数传送其他数据。 选择该模块时，这些参数将出现在机器学习 UI 最右侧的“属性”窗格中。 参数可以是任何受支持的类型，或者可按需创建自定义枚举。 与**端口**元素相似，**参数**元素可拥有一个可选**描述**元素，该元素可指定鼠标悬停在参数名上时会出现的文本。
可将 defaultValue、minValue 和 maxValue 等模块可选属性作为**属性**元素的参数添加到任意参数。 **属性**元素的有效属性取决于参数类型，该属性在下一节的受支持参数中有所描述。 对于 **isOptional** 属性设置为 **"true"** 的参数，用户无需输入值。 如果未向参数提供值，那么不会将该参数传送到入口点函数。 函数需要对入口点函数的可选参数进行显示处理，例如，向入口点函数定义分配一个默认的 NULL 值。 如果用户提供了值，可选参数仅会对其他约束参数，即最小或最大。
与输入和输出一样，有一点至关重要：每个参数必须拥有一个与其关联的唯一 ID 值。 在快速入门示例中，关联的 ID/参数为 swap。

### <a name="arg-element"></a>参数元素
使用 XML 定义文件**参数**部分的**参数**子元素定义模块参数。 与**端口**部分的子元素一样，**参数**部分的参数顺序可定义 UX 中的布局。 参数在 UI 中从上到下排列，排列顺序与其在 XML 文件中进行定义的顺序相同。 此处列出了机器学习支持的参数类型。 

**int** – 整数（32 位）类型参数。

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* 可选属性：**min**，**max**，**default** 和 **isOptional**

**double** – 双参数类型。

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* 可选属性：**min**，**max**，**default** 和 **isOptional**

**bool** – 由 UX 中复选框表示的布尔参数。

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* 可选属性：**default** - 若未设置则为 false

**字符串**：标准字符串

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* 可选属性：**default** 和 **isOptional**

**ColumnPicker**：列选择参数。 此类型在 UX 中显示为列选择器。 此处的**属性**元素用于指定端口 ID，将从此端口选择列，其中目标端口类型必须为 *DataTable*。 列选择结果以字符串列表传送到 R 函数，其中包括选中列的名称。 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* 必需属性：**portId** - 将输入元素 ID 与 *DataTable* 类型相匹配。
* 可选属性：
  
  * **allowedTypes** - 从可选列类型进行筛选。 有效值包括： 
    
    * 数字
    * 布尔
    * 分类
    * String
    * 标签
    * 功能
    * 得分
    * 全部
  * **默认** - 列选取器的有效默认选择包括： 
    
    * 无
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * 全部

**下拉列表**：用户指定的枚举（下拉）列表。 使用**项**元素在**属性**元素中指定下拉列表项。 每个**项**的 **ID** 必须是唯一的有效的 R 变量。 **项****名称**的值作为显示的文本以及传送到 R 函数的值。

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* 可选属性：
  * **默认** - 默认属性的值必须与**项**元素中的一个 ID 值相对应。

### <a name="auxiliary-files"></a>辅助文件
执行期间可使用放置在自定义模块 ZIP 文件中的所有文件。 将保留出现的所有目录结构。 这表明在本地和 Azure 机器学习执行中文件寻源方式相同。 

> [!NOTE]
> 请注意所有文件均被提取到了‘src’目录，因此所有路径必须有‘src’前缀。
> 
> 

例如，你想从数据集中删除带有 NA 的行，在将其输出到 CustomAddRows 之前，已将具有此功能的 R 函数写入了 RemoveDupNARows.R 文件：

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
可向 CustomAddRows 函数中的 RemoveDupNARows.R辅助文件发送消息：

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

然后，上传包含“CustomAddRows.R”、“CustomAddRows.xml”和“RemoveDupNARows.R”的 zip 文件作为自定义 R 模块。

## <a name="execution-environment"></a>执行环境
R 脚本的执行环境使用与**执行 R 脚本**模块相同的 R 版本，且可以使用相同的默认包。 还可以将 R 包加入自定义模块 zip 文件，将其添加到自定义模块。 只需按需将其加载到自己的 R 环境中的 R 脚本中。 

**执行环境限制**包括：

* 非持久文件系统：自定义模块运行的文件编写无法在相同模块的多个运行上持续进行。
* 无网络访问

