---
title: 应用数学运算
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习 service 中的 "应用数学运算" 模块将数学运算应用于管道中的列值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 49bab338f559b1b43389e12d98c75bbffbb25a85
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694761"
---
# <a name="apply-math-operation"></a>应用数学运算

本文介绍 Azure 机器学习可视界面的模块。

使用 "应用数学运算" 可创建应用于输入数据集中数值列的计算。 

支持的数学运算包括常见算术函数，如乘法和除法、三角函数、各种舍入函数，以及在数据科学中使用的特殊函数，如伽玛和 error 函数。  

定义操作并运行管道后，会将这些值添加到数据集。 根据配置模块的方式，您可以：

+ 将结果追加到数据集。 当验证操作的结果时，此方法特别有用。
+ 将列的值替换为新的计算值。
+ 为结果生成新列，而不显示原始数据。 

在以下类别中查找所需的操作：  

- [基本](#basic-math-operations)  
  
     "**基本**" 类别中的函数可用于操作值的单个值或列值。 例如，您可以获取列中所有数字的绝对值，或者计算列中每个值的平方根。  
  
-   [比较](#comparison-operations)  
  
      "**比较**" 类别中的函数全部用于比较：可以对两个列中的值进行成对比较，也可以将列中的每个值与指定的常量进行比较。 例如，您可以对列进行比较，以确定两个数据集中的值是否相同。 或者，您可以使用常量（例如最大允许值）来查找数值列中的离群值。  
  
-   [操作](#arithmetic-operations)  
  
     **操作**类别包含基本数学函数：加法、减法、乘法和除法。 您可以使用列或常量。 例如，可以将列 A 中的值添加到 B 列中的值。或者，您可以从 A 列中的每个值减去一个常数，例如之前计算的平均值。  
  
-   [四舍五入](#rounding-operations)  
  
     **舍入**类别包括各种函数，可用于对各种精度级别执行舍入、天花板、楼层和截断等操作。 可以指定小数和整数的精度级别。  
  
-   [专题](#special-math-functions)  
  
     **特殊**类别包括特别适用于数据科学的数学函数，如椭圆积分和高斯错误函数。  
  
-   [三角](#trigonometric-functions)  
  
     **三角函数**类别包括所有标准三角函数。 例如，可以将弧度转换为度，或将计算函数（如弧度或度）转换为弧度。
     这些函数是一元的，这意味着它们采用单个值列作为输入，应用三角函数，并返回一列值作为结果。  因此，您需要确保输入列是适当的类型并且包含指定操作的正确类型的值。   

## <a name="how-to-configure-apply-math-operation"></a>如何配置应用数学运算  

"**应用数学运算**" 模块需要一个数据集，其中至少包含一个包含数字的列。 数字可以是离散的，也可以是连续的，但必须是数值数据类型，而不是字符串。

您可以对多个数值列应用相同的操作，但所有列必须位于同一个数据集中。 

此模块的每个实例一次只能执行一种类型的操作。 若要执行复杂的数学运算，你可能需要将**应用数学操作**模块的多个实例链接在一起。  
  
1.  向管道添加 "**应用数学运算**" 模块。

1. 连接包含至少一个数值列的数据集。  

1.  选择要对其执行计算的一个或多个源列。   
  
    - 您选择的任何列都必须是数值数据类型。 
    - 数据范围必须对选定的数学运算有效。 否则，可能会出现错误或 NaN （不是数字）结果。 例如，Ln （-1.0）是无效的操作，将导致值 `NaN`。
  
1.  单击 "**类别**" 以选择要执行的数学运算**类型**。
    
1. 从该类别的列表中选择特定的操作。
  
1.  设置每种操作所需的其他参数。  
  
1.  使用 "**输出模式**" 选项指示如何生成数学运算： 

    - **追加**。 用作输入的所有列都包括在输出数据集中，另外还会追加一个包含数学运算结果的附加列。
    - **就地**。 用作输入的列中的值将替换为新的计算值。 
    - **ResultOnly**。 返回一列，其中包含数学运算的结果。
  
1.  运行管道。  
  
## <a name="results"></a>结果

如果使用**Append**或**ResultOnly**选项生成结果，则返回的数据集的列标题指示操作和所使用的列。 例如，如果使用**Equals**运算符比较两个列，结果将如下所示：  
  
-   **Equals （Col2_Col1）** ，表示您针对 Col1 测试了 Col2。  
-   **Equals （Col2_ $ 10）** ，表示将第2列与常量10进行比较。  

即使使用**就地**选项，也不会删除或更改源数据;原始数据集中的列仍可用于可视界面。 若要查看原始数据，可以连接 "[添加列](add-columns.md)" 模块并将其联接到 "**应用数学" 操作**的输出。  
    
## <a name="basic-math-operations"></a>基本数学运算 

"**基本**" 类别中的函数通常采用列中的单个值，执行预定义的操作，并返回单个值。 对于某些函数，可以指定常量或设置为第二个参数的列。  
  
 Azure 机器学习支持 "**基本**" 类别中的以下函数：  

### <a name="abs"></a>Abs

返回选定列的绝对值。  
  
### <a name="atan2"></a>Atan2

返回四象限反正切值。  

选择包含点坐标的列。 对于第二个自变量，该参数对应于 x 坐标，还可以指定常量。  

对应于 Matlab 中的 ATAN2 函数。  

### <a name="conj"></a>Conj

返回所选列中的值的共轭。  

### <a name="cuberoot"></a>CubeRoot

计算所选列中的值的多维数据集根。  

### <a name="doublefactorial"></a>DoubleFactorial  
 计算所选列中的值的双阶乘。 双阶乘是正常阶乘函数的扩展，表示为 x!!。  

### <a name="eps"></a>Eps

返回当前值和下一最大双精度数字之间的间距大小。 对应于 Matlab 中的 EPS 函数。  
  
### <a name="exp"></a>.Exp

返回 e 的在所选列中的值的幂。 这与 Excel EXP 函数相同。  

### <a name="exp2"></a>Exp2

返回参数的以2为底的指数，其中 t 为 y = x * 2<sup>t</sup> ，其中 t 是包含指数的值的列。  

在 "**列集**" 中，选择包含指数值 t 的列。

对于**Exp2** ，可以指定第二个参数 x，该参数可以是常量，也可以是其他值列。 在 "**第二个参数类型**" 中，指示是将乘数 x 作为常量还是列中的值提供。  

例如，如果选择一个列，其中的值同时为乘数和指数 {0,1,2,3,4,5}，则该函数将返回 {0，2，8，24，64 160）。  

### <a name="expminus1"></a>ExpMinus1 

返回所选列中的值的负指数。  

### <a name="factorial"></a>阶乘
返回所选列中的值的阶乘。  

### <a name="hypotenuse"></a>斜边
为一个三角形计算斜边，其中一方的长度指定为值的一列，而另一端的长度指定为一个常量或两列。  

### <a name="ln"></a>Ln

返回所选列中的值的自然对数。  

### <a name="lnplus1"></a>LnPlus1

返回所选列中的值的自然对数加1。  

### <a name="log"></a>日志

返回所选列中的值的日志，前提是指定了 base。  

您可以将基本（第二个参数）指定为常量，也可以选择其他值列。  

### <a name="log10"></a>Log10

返回所选列的以10为底的对数值。  

### <a name="log2"></a>Log2

返回所选列的以2为底的对数值。  

### <a name="nthroot"></a>NthRoot
使用指定的 n 返回值的第 n 个根。  

使用**列集**选项选择要为其计算根的列。  

在 "**第二个参数类型**" 中，选择包含根的另一列，或指定要用作根的常量。  

如果第二个参数是列，则列中的每个值将用作对应行的 n 的值。 如果第二个参数为常量，请在**第二个参数**文本框中键入 n 的值。
### <a name="pow"></a>Pow

计算所选列中每个值的 X 的次幂。  

首先，通过使用**列集**选项，选择包含**基数**（应为 float）的列。  

在 "**第二个参数类型**" 中，选择包含指数的列，或指定要用作指数的常量。  

如果第二个参数是列，则列中的每个值将用作对应行的指数。 如果第二个参数为常量，请在 "**第二个参数**" 文本框中键入指数值。  

### <a name="sqrt"></a>Sqrt

返回所选列中的值的平方根。  

### <a name="sqrtpi"></a>SqrtPi

对于所选列中的每个值，将值乘以 pi，然后返回结果的平方根。  

### <a name="square"></a>Square

将所选列中的值作为平方。  

## <a name="comparison-operations"></a>比较运算  

只要需要测试两组值，都可以使用 Azure 机器学习可视化界面中的比较函数。 例如，在管道中，你可能需要执行以下比较操作：  

- 根据阈值计算概率评分模型的列。
- 确定两个结果集是否相同。 对于每个不同的行，添加一个可用于进一步处理或筛选的 FALSE 标志。  

### <a name="equalto"></a>EqualTo

如果值相同，则返回 True。  

### <a name="greaterthan"></a>GreaterThan

如果**列集中**的值大于指定的常量，或大于比较列中的相应值，则返回 True。  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

如果**列集中**的值大于或等于指定的常量，或者大于等于比较列中的相应值，则返回 True。  

### <a name="lessthan"></a>LessThan

如果 "**列集**" 中的值小于指定的常量，或小于比较列中的相应值，则返回 True。  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

如果 "**列集**" 中的值小于或等于指定的常量，或者小于或等于比较列中的相应值，则返回 True。  

### <a name="notequalto"></a>NotEqualTo

如果 "**列集**" 中的值不等于常量或比较列，则返回 True; 如果相等，则返回 False。  

### <a name="pairmax"></a>PairMax

返回大于（**列集中**的值或常量或比较列中的值）的值。  

### <a name="pairmin"></a>PairMin

返回小于的值（**列集中**的值或常量或比较列中的值）  
  
##  <a name="arithmetic-operations"></a>算术运算   

包括基本算术运算：加法和减法、除法和乘法。  由于大多数操作都是二进制，需要两个数字，因此您首先选择操作，然后选择要在第一个和第二个参数中使用的一个或多个列。

选择除法和减法列的顺序可能看起来不够直观。但是，为了更容易理解结果，列标题提供操作名称和列的使用顺序。

Operation|Num1|Num2|结果列|结果值|
----|----|----|----|----
|加|第|5|添加（Num2_Num1）| 4|
|乘|第|5|多（Num2_Num1）|5|
|减|第|5|减法（Num2_Num1）|4|
|减|0|第|减法（Num2_Num1）|0|
|除|第|5|除（Num2_Num1）|5|
|除|0|第|除（Num2_Num1）|Infinity|

### <a name="add"></a>添加

使用**列集**指定源列，然后将值添加到**第二个参数**中指定的数字。  

若要添加两个列中的值，请使用**列集**选择一个或多个列，然后使用**第二个参数**选择第二列。  

### <a name="divide"></a>除

**将列集中**的值除以一个常量，或除以**第二个参数**中定义的列值。  换句话说，您首先选取除数，然后选择被除数。 输出值为商。

### <a name="multiply"></a>乘

**将列集中**的值乘以指定的常量或列值。  

### <a name="subtract"></a>减

使用**列 set**选项，通过选择不同的列来指定要操作的值列（*被减数*）。 然后，通过使用**第二个参数**下拉列表指定要减去的数字（*减数*）。 您可以选择一个常量或值列。

##  <a name="rounding-operations"></a>舍入运算 

Azure 机器学习视觉对象界面支持各种舍入操作。 对于许多操作，必须指定舍入时要使用的精度。 可以使用指定为常量的静态精度级别，也可以应用从值列获取的动态精度值。  

- 如果使用常量，则将 "**精度类型**" 设置为 "**常量**"，然后在 "**常量精度**" 文本框中键入数字的位数。 如果键入一个非整数，则该模块不会引发错误，但可能会产生意外的结果。  

- 若要为数据集中的每个行使用不同的精度值，请将 "**精度类型**" 设置为**列集**，然后选择包含相应精度值的列。  

### <a name="ceiling"></a>顶角

返回**列集中**的值的上限。  

### <a name="ceilingpower2"></a>CeilingPower2

返回**列集中**的值的方差。  

### <a name="floor"></a>层

将**列集中**值的下限返回到指定的精度。  

### <a name="mod"></a>Mod

将**列集中**的值的小数部分返回到指定的精度。  

### <a name="quotient"></a>之

将**列集中**的值的小数部分返回到指定的精度。  

### <a name="remainder"></a>剩下

返回**列集中**的值的余数。  

### <a name="rounddigits"></a>RoundDigits

返回**列集中**的值，由4/5 规则舍入到指定的位数。  

### <a name="rounddown"></a>RoundDown

返回 "**列集**" 中的值，并向下舍入到指定的位数。  

### <a name="roundup"></a>RoundUp

返回**列集中**的值，向上舍入到指定的位数。  

### <a name="toeven"></a>ToEven

返回 "**列集**" 中的值，舍入为最接近的整数，偶数。  

### <a name="toodd"></a>ToOdd

返回 "**列集**" 中的值，舍入为最接近的整数，奇数。  

### <a name="truncate"></a>去

通过删除指定精度不允许的所有位数，截断**列集中**的值。  
  
## <a name="special-math-functions"></a>特殊数学函数

此类别包括通常用于数据科学的特殊数学函数。 除非另有说明，否则函数为一元，并为所选列中的每个值返回指定的计算。  

### <a name="beta"></a>Beta

返回欧拉的 beta 函数的值。  

### <a name="ellipticintegrale"></a>EllipticIntegralE
返回不完整椭圆整数的值。  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

返回完整的椭圆整数（K）的值。  

### <a name="erf"></a>Erf

返回错误函数的值。  

Error 函数（也称为高斯错误函数）是 sigmoid 形状的特殊功能，用于描述分散。  

### <a name="erfc"></a>Erfc

返回互补错误函数的值。  

Erfc 定义为1– erf （x）。  

### <a name="erfscaled"></a>ErfScaled

返回缩放错误函数的值。  

可以使用错误函数的缩放版本来避免算术下溢。  

### <a name="erfinverse"></a>ErfInverse

返回反向 erf 函数的值。  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

返回指数整型值的值。  

### <a name="gamma"></a>Γ

返回伽玛函数的值。  

### <a name="gammaln"></a>GammaLn

返回伽玛函数的自然对数。  

### <a name="gammaregularizedp"></a>GammaRegularizedP

返回正则化不完整伽玛函数的值。  

此函数采用第二个参数，可将其作为一个常量或一列值提供。  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

返回反向正则化不完整伽玛函数的值。  

此函数采用第二个参数，可将其作为一个常量或一列值提供。  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

返回正则化不完整伽玛函数的值。  

此函数采用第二个参数，可将其作为一个常量或一列值提供。  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

返回反向通用化正则化不完整伽玛函数的值。

此函数采用第二个参数，可将其作为一个常量或一列值提供。  

### <a name="polygamma"></a>PolyGamma

返回 polygamma 函数的值。  

此函数采用第二个参数，可将其作为一个常量或一列值提供。

##  <a name="trigonometric-functions"></a>三角函数 

此类别 iIncludes 大部分重要的三角函数和反三角函数。 所有三角函数均为一元函数，不需要其他参数。  

### <a name="acos"></a>Acos

计算列值的反余弦值。  

### <a name="acosdegree"></a>AcosDegree

计算列值的反余弦值（以度为单位）。  

### <a name="acosh"></a>Acosh

计算列值的双曲反余弦值。  

### <a name="acot"></a>Acot

计算列值的 number。  

### <a name="acotdegrees"></a>AcotDegrees

计算列值的 number，以度为单位。  

### <a name="acoth"></a>Acoth

计算列值的双曲 number 值。  

### <a name="acsc"></a>Acsc

计算列值的 arccosecant。  

### <a name="acscdegrees"></a>AcscDegrees

计算列值的 arccosecant，以度为单位。  
### <a name="asec"></a>Asec

计算列值的反正割值。  

### <a name="asecdegrees"></a>AsecDegrees

计算列值的反正割值，以度为单位。  

### <a name="asech"></a>Asech

计算列值的双曲反正割值值。  

### <a name="asin"></a>Asin

计算列值的反正弦值。  

### <a name="asindegrees"></a>AsinDegrees

计算列值的反正弦值（以度为单位）。  

### <a name="asinh"></a>Asinh

计算列值的双曲反正弦值。  

### <a name="atan"></a>Atan

计算列值的反正切值。  

### <a name="atandegrees"></a>AtanDegrees

计算列值的反正切值（以度为单位）。  

### <a name="atanh"></a>Atanh

计算列值的双曲反正切值。  

### <a name="cos"></a>缆

计算列值的余弦值。  

### <a name="cosdegrees"></a>CosDegrees

计算列值的余弦值（以度为单位）。  

### <a name="cosh"></a>Cosh

计算列值的双曲余弦值。  

### <a name="cot"></a>Cot

计算列值的余切值。  

### <a name="cotdegrees"></a>CotDegrees

计算列值的余切值（以度为单位）。  

### <a name="coth"></a>Coth
计算列值的双曲余切值。  

### <a name="csc"></a>Csc.exe

计算列值的余割值。  

### <a name="cscdegrees"></a>CscDegrees

计算列值的余割值（以度为单位）。  

### <a name="csch"></a>Csch

计算列值的双曲余割值。  

### <a name="degreestoradians"></a>DegreesToRadians

将度转换为弧度。  

### <a name="sec"></a>秒

计算列值的正割值。  

### <a name="asecdegrees"></a>aSecDegrees

计算列值的正割值（以度为单位）。  

### <a name="asech"></a>aSech

计算列值的双曲正割值。  

### <a name="sign"></a>签名

返回列值的符号。  

### <a name="sin"></a>Sin

计算列值的正弦值。  

### <a name="sinc"></a>由于

计算列值的正弦余弦值。  

### <a name="sindegrees"></a>SinDegrees

计算列值的正弦值（以度为单位）。  

### <a name="sinh"></a>Sinh

计算列值的双曲正弦值。  

### <a name="tan"></a>tan

计算列值的正切值。  

### <a name="tandegrees"></a>TanDegrees

计算参数的正切值（以度为单位）。  

### <a name="tanh"></a>Tanh

计算列值的双曲正切值。  
  
## <a name="technical-notes"></a>技术说明

选择多个列作为第二个运算符时，请小心。 如果操作很简单，例如向所有列添加常量，则可以很容易地了解结果。 

假设你的数据集包含多个列，并且你将该数据集添加到其自身。 在结果中，每个列都添加到其自身，如下所示：  
  
|Num1|Num2|Num3|添加（Num1_Num1）|添加（Num2_Num2）|添加（Num3_Num3）|
|----|----|----|----|----|----|
|第|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|第|-1|0|2|-2|

如果需要执行更复杂的计算，可以链接多个**应用数学运算**的实例。 例如，您可以使用一个 "**应用数学运算**" 实例来添加两个列，然后使用 "**应用数学运算**" 的另一个实例来将和除以一个常量，以获取平均值。  
  
或者，使用以下模块之一，使用 SQL、R 或 Python 脚本一次执行所有计算：
 
+ [执行 R 脚本](execute-r-script.md)
+ [执行 Python 脚本](execute-python-script.md)
+ [应用 SQL 转换](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
