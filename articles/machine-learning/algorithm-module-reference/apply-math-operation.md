---
title: 应用数学运算
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的”应用数学运算”模块将数学运算应用于管道中的列值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 04ce45f428604275696d83938708bcee0c6c023f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536760"
---
# <a name="apply-math-operation"></a>应用数学运算

本文介绍 Azure 机器学习设计器的某个模块。

使用“应用数学运算”可创建应用于输入数据集中数值列的计算。 

数学运算包括算术函数、三角函数函数、舍入函数，以及在数据科学中使用的特殊函数，如伽玛和 error 函数。  

定义运算并运行管道后，值会添加到数据集。 根据配置模块的方式，可以执行以下操作：

+ 在验证操作) 的结果时，将结果追加到数据集 (有用。
+ 将列的值替换为新的计算值。
+ 为结果生成新列，而不显示原始数据。 

在以下类别中查找所需的操作：  

- [基本](#basic-math-operations)  
  
     “基本****”类别中的函数用于处理单个值或值列。 例如，可以获取列中所有数字的绝对值，或者计算列中每个值的平方根。  
  
-   [比较](#comparison-operations)  
  
      “比较”**** 类别中的函数全部用于比较：可以对两个列中的值进行成对比较，也可以将列中的每个值与指定的常量进行比较。 例如，可以对列进行比较，以确定两个数据集中的值是否相同。 或者，可以使用常量（例如最大允许值）来查找数值列中的异常值。  
  
-   [操作](#arithmetic-operations)  
  
     **操作**类别包括基本的数学函数：加法、减法、乘法和除法。 可以使用列或常量。 例如，可以将列 A 中的值添加到 B 列中的值。或者，可以将列 A 中的每个值减去一个常数，例如之前计算的平均值。  
  
-   [舍入](#rounding-operations)  
  
     “舍入”**** 类别包含多种用于执行运算（例如，根据不同精度级别舍入、向下取整和截断）的函数。 可以指定小数和整数的精度级别。  
  
-   [专题](#special-math-functions)  
  
     “特殊”**** 类别包括特别用于数据科学的数学函数，如椭圆积分和高斯误差函数。  
  
-   [三角](#trigonometric-functions)  
  
     “三角”**** 类别包括所有标准三角函数。 例如，可以将弧度转换为度，或以弧度或度计算函数（例如切线）。
     这些函数是一元的，这意味着它们采用单个值列作为输入，应用三角函数，并返回一列值作为结果。 确保输入列是适当的类型，并且包含指定操作的正确类型的值。   

## <a name="how-to-configure-apply-math-operation"></a>如何配置“应用数学运算”  

“应用数学运算”**** 模块需要包含至少一个包含仅数字的列的数据集。 数字可以是离散的，也可以是连续的，但必须是数值数据类型，而不是字符串。

可以对多个数值列应用相同的运算，但所有列必须位于同一个数据集中。 

此模块的每个实例一次只能执行一种类型的运算。 若要执行复杂的数学运算，可能需要将“应用数学运算”模块的多个实例链接在一起****。  
  
1.  将“应用数学运算”模块添加到管道****。

1. 连接包含至少一个数值列的数据集。  

1.  选择要对其执行计算的一个或多个源列。   
  
    - 你选择的任何列都必须是数值数据类型。 
    - 数据范围必须对选定的数学运算有效。 否则，可能会出现错误或 NaN（不是数字）结果。 例如，Ln(-1.0) 是无效运算，生成值 `NaN`。
  
1.  选择 " **类别** " 以选择要执行的数学运算 **类型** 。
    
1. 从该类别的列表中选择特定的运算。
  
1.  设置每种运算所需的其他参数。  
  
1.  使用“输出模式”选项来指示要生成数学运算的方式****： 

    - **Append**。 用作输入的所有列都包括在输出数据集中，另外还会追加一个包含数学运算结果的附加列。
    - **就地**。 用作输入的列中的值将替换为新的计算值。 
    - **ResultOnly**。 返回一列，其中包含数学运算的结果。
  
1.  提交管道。  
  
## <a name="results"></a>结果

如果使用“Append”或“ResultOnly”选项生成结果，则返回的数据集的列标题指示使用的运算和列********。 例如，如果使用“Equals”运算符比较两个列，结果将如下所示****：  
  
-   Equals(Col2_Col1)，表示针对 Col1 测试了 Col2****。  
-   Equals(Col2_$10)，表示将列 2 与常数 10 比较****。  

即使使用 **就地** 选项，也不会删除或更改源数据;原始数据集中的列仍可用于设计器中。 若要查看原始数据，可以连接“添加列”模块，并将其联接到“应用数学运算”的输出[](add-columns.md)****。  
    
## <a name="basic-math-operations"></a>基本数学运算 

“基本”类别中的函数通常采用列中的单个值，执行预定义的运算，并返回单个值****。 对于某些函数，可以指定常量或将列集指定为第二个参数。  
  
 Azure 机器学习支持“基本”类别中的以下函数****：  

### <a name="abs"></a>Abs

返回所选列的绝对值。  
  
### <a name="atan2"></a>Atan2

返回四象限反正切值。  

选择包含点坐标的列。 对于第二个参数（该参数对应于 x 坐标），还可以指定常量。  

对应于 MATLAB 中的 ATAN2 函数。  

### <a name="conj"></a>Conj

返回所选列中的值的共轭。  

### <a name="cuberoot"></a>CubeRoot

计算所选列中的值的立方根。  

### <a name="doublefactorial"></a>DoubleFactorial  
 计算所选列中的值的双阶乘。 双阶乘是正常阶乘函数的扩展，以 `x!!` 表示。  

### <a name="eps"></a>Eps

返回当前值和下一最大双精度数字之间的间距大小。 对应于 MATLAB 中的 EPS 函数。  
  
### <a name="exp"></a>Exp

返回以 e 为底、以所选列中值为幂的数值。 此函数与 Excel EXP 函数相同。  

### <a name="exp2"></a>Exp2

返回自变量与以 2 为底的指数次方的乘积，求解方式为 y = x * 2<sup>t</sup>，其中 t 是包含指数的值所在列。  

在列集中，选择包含指数值的列 t****。

对于 Exp2，可以指定第二个自变量 x，该自变量可以是常量，也可以是其他值列****。 在“第二个自变量类型”中，指示是将乘数 x 作为常量还是列中的值提供****。  

例如，如果选择一个列，其中的值 {0,1,2,3,4,5} 同时用作乘数和指数，则该函数将返回 {0, 2, 8, 24, 64 160)。  

### <a name="expminus1"></a>ExpMinus1 

返回所选列中的值的负指数。  

### <a name="factorial"></a>阶乘
返回所选列中的值的阶乘。  

### <a name="hypotenuse"></a>斜边
计算三角形的斜边，该三角形的一条边的长度指定为一个值列，另一条边的长度指定为常量或两列。  

### <a name="ln"></a>自然对数

返回所选列中的值的自然对数。  

### <a name="lnplus1"></a>LnPlus1

返回所选列中的值的自然对数加 1。  

### <a name="log"></a>日志

返回所选列中的值的对数（在指定底数的情况下）。  

可以将底数（第二个参数）指定为常量，也可以选择其他值列。  

### <a name="log10"></a>Log10

返回所选列的以10为底的对数值。  

### <a name="log2"></a>Log2

返回所选列的以2为底的对数值。  

### <a name="nthroot"></a>NthRoot
返回值的指定次根。  

使用 ColumnSet 选项，选择要计算根的列****。  

在“第二个自变量类型”中，选择包含根的另一列，或指定要用作根的常量****。  

如果第二个自变量是列，则列中的每个值将用作对应行的 n 的值。 如果第二个自变量为常量，请在“第二个自变量”文本框中键入 n 的值****。
### <a name="pow"></a>Pow

对于所选列中的每个值，计算 X 的 Y 次幂。  

首先，通过使用 ColumnSet 选项，选择包含底数（应该为浮点值）的列********。  

在“第二个自变量类型”中，选择包含指数的列，或指定要用作指数的常量****。  

如果第二个自变量是列，则列中的每个值将用作对应行的指数。 如果第二个自变量为常量，请在“第二个自变量”文本框中键入指数的值****。  

### <a name="sqrt"></a>Sqrt

返回所选列中的值的平方根。  

### <a name="sqrtpi"></a>SqrtPi

对于所选列中的每个值，将值乘以 pi，然后返回结果的平方根。  

### <a name="square"></a>Square

计算所选列中的值的平方。  

## <a name="comparison-operations"></a>比较运算  

如果需要对比两组值大小，可以使用 Azure 机器学习设计器中的比较函数。 例如，在管道中，你可能需要执行以下比较运算：  

- 针对阈值对概率分数模型列求值。
- 确定两个结果集是否相同。 对于每个不同的行，添加一个可用于进一步处理或筛选的 FALSE 标志。  

### <a name="equalto"></a>EqualTo

如果值相同，返回 True。  

### <a name="greaterthan"></a>GreaterThan

如果列集中的值大于指定常量或大于比较列中的相应值，返回 True****。  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

如果列集中的值大于或等于指定常量或者大于或等于比较列中的相应值，返回 True****。  

### <a name="lessthan"></a>LessThan

如果列集中的值小于指定常量或小于比较列中的相应值，返回 True****。  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

如果列集中的值小于或等于指定常量或者小于或等于比较列中的相应值，返回 True****。  

### <a name="notequalto"></a>NotEqualTo

如果“列集”**** 中的值不等于常量或比较列，返回 True；否则，返回 False。  

### <a name="pairmax"></a>PairMax

返回两者中较大的值：**“列集”** 中的值或者常量或比较列中的值。  

### <a name="pairmin"></a>PairMin

返回较小的值 - 列集中的值或者常量或比较列中的值****  
  
##  <a name="arithmetic-operations"></a>算术运算   

包括基本算术运算：加法和减法、除法和乘法。  由于大多数运算都是二进制，需要两个数字，因此需要首先选择运算，然后选择要在第一个和第二个自变量中使用的列或数字。

选择除法和减法列的顺序可能看起来不够直观。但是，为了更容易理解结果，列标题提供运算名称和列的使用顺序。

操作|Num1|Num2|结果列|结果值|
----|----|----|----|----
|加法|1|5|Add(Num2_Num1)| 4|
|乘法|1|5|Multiple(Num2_Num1)|5|
|减法|1|5|Subtract(Num2_Num1)|4|
|减法|0|1|Subtract(Num2_Num1)|0|
|部门|1|5|Divide(Num2_Num1)|5|
|部门|0|1|Divide(Num2_Num1)|无限|

### <a name="add"></a>添加

使用列集指定源列，然后将这些值加上“第二个自变量”中指定的数字********。  

若要添加两个列中的值，请使用列集选择一个或多个列，然后使用“第二个自变量”选择第二个列********。  

### <a name="divide"></a>除

将列集中的值除以一个常量，或除以“第二个自变量“中定义的列值********。  换句话说，首先选取除数，然后选择被除数。 输出值为商。

### <a name="multiply"></a>乘

将“列集”**** 中的值与指定常量或列值相乘。  

### <a name="subtract"></a>减

使用列集选项，指定要进行运算的值列（被减数）******。 然后，使用“第二个自变量”下拉列表指定要减去的数（减数）******。 可以选择一个常量或值列。

##  <a name="rounding-operations"></a>舍入运算 

Azure 机器学习设计器支持各种舍入运算。 对于许多运算，必须指定舍入时要使用的精度。 可以使用指定为常量的静态精度级别，也可以应用从值列获取的动态精度值。  

- 如果使用常量，请将“精度类型”设置为“常量”，然后在“常量精度”文本框中键入以整数表示的位数************。 如果键入一个非整数，该模块不会引发错误，但可能会产生意外的结果。  

- 若要为数据集中的每行使用不同的精度值，请将“精度类型”设置为“列集”，然后选择包含相应精度值的列********。  

### <a name="ceiling"></a>Ceiling

返回“列集”**** 中的值的上限。  

### <a name="ceilingpower2"></a>CeilingPower2

返回“列集”**** 中的值的上限平方值。  

### <a name="floor"></a>Floor

以指定精度返回“列集”**** 中的值的下限。  

### <a name="mod"></a>Mod

以指定精度返回“列集”**** 中的值的小数部分。  

### <a name="quotient"></a>商

以指定精度返回“列集”**** 中的值的小数部分。  

### <a name="remainder"></a>余数

返回“列集”**** 中的值的其余部分。  

### <a name="rounddigits"></a>RoundDigits

返回“列集”**** 中的值，并按舍入规则舍入到指定位数。  

### <a name="rounddown"></a>RoundDown

返回“列集”**** 中的值，并向下舍入到指定位数。  

### <a name="roundup"></a>RoundUp

返回“列集”**** 中的值，并向上舍入到指定位数。  

### <a name="toeven"></a>ToEven

返回“列集”**** 中的值，并舍入到最近的偶整数。  

### <a name="toodd"></a>ToOdd

返回“列集”**** 中的值，并舍入到最近的奇整数。  

### <a name="truncate"></a>Truncate

通过删除指定精度不允许的所有位数截断“列集”**** 中的值。  
  
## <a name="special-math-functions"></a>特殊数学函数

此类别包括通常用于数据科学的特殊数学函数。 除非另有说明，否则函数为一元函数，并为所选列中的每个值返回指定的计算。  

### <a name="beta"></a>Beta

返回欧拉的 beta 函数的值。  

### <a name="ellipticintegrale"></a>EllipticIntegralE
返回不完整椭圆整数的值。  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

返回完整椭圆整数 (K) 的值。  

### <a name="erf"></a>Erf

返回误差函数的值。  

误差函数（也称为高斯误差函数）是 sigmoid 形状的特殊函数，用于描述扩散概率。  

### <a name="erfc"></a>Erfc

返回互补误差函数的值。  

`Erfc` 定义为1– erf (x) 。  

### <a name="erfscaled"></a>ErfScaled

返回刻度误差函数的值。  

误差函数的刻度版本可用于避免算术下溢。  

### <a name="erfinverse"></a>ErfInverse

返回反转函数的值 `erf` 。  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

返回指数整数 Ei 的值。  

### <a name="gamma"></a>Gamma

返回伽马函数的值。  

### <a name="gammaln"></a>GammaLn

返回伽马函数的自然对数。  

### <a name="gammaregularizedp"></a>GammaRegularizedP

返回正则化不完整伽马函数的值。  

此函数采用第二个自变量，可将其作为一个常量或一个值列提供。  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

返回正则化不完整伽马函数的反函数的值。  

此函数采用第二个自变量，可将其作为一个常量或一个值列提供。  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

返回正则化不完整伽马函数的值。  

此函数采用第二个自变量，可将其作为一个常量或一个值列提供。  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

返回广义正则化不完整伽马函数的反函数的值。

此函数采用第二个自变量，可将其作为一个常量或一个值列提供。  

### <a name="polygamma"></a>PolyGamma

返回多伽玛函数的值。  

此函数采用第二个自变量，可将其作为一个常量或一个值列提供。

##  <a name="trigonometric-functions"></a>三角函数 

此类别包含大部分重要的三角函数和反三角函数。 所有三角函数均为一元函数，不需要其他自变量。  

### <a name="acos"></a>Acos

计算列值的反余弦值。  

### <a name="acosdegree"></a>AcosDegree

计算列值的反余弦值（以度为单位）。  

### <a name="acosh"></a>Acosh

计算列值的双曲反余弦值。  

### <a name="acot"></a>Acot

计算列值的反余切。  

### <a name="acotdegrees"></a>AcotDegrees

计算列值的反余切（以度为单位）。  

### <a name="acoth"></a>Acoth

计算列值的双曲反余切值。  

### <a name="acsc"></a>Acsc

计算列值的反余割。  

### <a name="acscdegrees"></a>AcscDegrees

计算列值的反余割（以度为单位）。  
### <a name="asec"></a>Asec

计算列值的反正割。  

### <a name="asecdegrees"></a>AsecDegrees

计算列值的反正割（以度为单位）。  

### <a name="asech"></a>Asech

计算列值的双曲反正割值。  

### <a name="asin"></a>Asin

计算列值的反正弦。  

### <a name="asindegrees"></a>AsinDegrees

计算列值的反正弦值（以度为单位）。  

### <a name="asinh"></a>Asinh

计算列值的双曲反正弦值。  

### <a name="atan"></a>Atan

计算列值的反正切。  

### <a name="atandegrees"></a>AtanDegrees

计算列值的反正切（以度为单位）。  

### <a name="atanh"></a>Atanh

计算列值的双曲反正切值。  

### <a name="cos"></a>Cos

计算列值的余弦。  

### <a name="cosdegrees"></a>CosDegrees

计算列值的余弦（以度为单位）。  

### <a name="cosh"></a>Cosh

计算列值的双曲余弦值。  

### <a name="cot"></a>Cot

计算列值的余切。  

### <a name="cotdegrees"></a>CotDegrees

计算列值的余切（以度为单位）。  

### <a name="coth"></a>Coth
计算列值的双曲余切值。  

### <a name="csc"></a>Csc

计算列值的余割。  

### <a name="cscdegrees"></a>CscDegrees

计算列值的余割（以度为单位）。  

### <a name="csch"></a>Csch

计算列值的双曲余割值。  

### <a name="degreestoradians"></a>DegreesToRadians

将度转换为弧度。  

### <a name="sec"></a>Sec

计算列值的正割。  

### <a name="asecdegrees"></a>aSecDegrees

计算列值的正割（以度为单位）。  

### <a name="asech"></a>aSech

计算列值的双曲正割值。  

### <a name="sign"></a>签名

返回列值的符号。  

### <a name="sin"></a>Sin

计算列值的正弦。  

### <a name="sinc"></a>Sinc

计算列值的正弦-余弦值。  

### <a name="sindegrees"></a>SinDegrees

计算列值的正弦（以度为单位）。  

### <a name="sinh"></a>Sinh

计算列值的双曲正弦值。  

### <a name="tan"></a>Tan

计算列值的正切值。  

### <a name="tandegrees"></a>TanDegrees

计算自变量的正切值（以度为单位）。  

### <a name="tanh"></a>Tanh

计算列值的双曲正切值。  
  
## <a name="technical-notes"></a>技术说明

选择多个列作为第二个运算符时，请小心。 如果运算很简单（例如向所有列添加常量），则可以很容易地理解结果。 

假设数据集包含多个列，你将该数据集添加到其自身。 在结果中，每个列都添加到其自身，如下所示：  
  
|Num1|Num2|Num3|Add(Num1_Num1)|Add(Num2_Num2)|Add(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

如果需要进行更复杂的计算，可以链接“应用数学运算”的多个实例****。 例如，你可能使用 **“应用数学运算”** 的一个实例添加两列，然后使用 **“应用数学运算”** 的另一实例将和除以一个常量，以获取平均值。  
  
或者，使用以下模块之一，使用 SQL、R 或 Python 脚本一次执行所有计算：
 
+ [执行 R 脚本](execute-r-script.md)
+ [执行 Python 脚本](execute-python-script.md)
+ [应用 SQL 转换](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
