---
title: "有关 Net# 神经网络规范语言的指南 | Microsoft 文档"
description: "有关 Net# 神经网络规范语言的语法，以及如何使用 Net# 在 Microsoft Azure ML 中创建自定义神经网络模型的示例"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeannt
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 54bef3e257363300ee1a13f7f45fc983e465ddbf
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>有关 Azure 机器学习的 Net# 神经网络规范语言的指南
## <a name="overview"></a>概述
Net# 是由 Microsoft 开发的一种用于定义神经网络体系结构的语言。 可以在 Microsoft Azure 机器学习的神经网络模块中使用 Net#。

<!-- This function doesn't currentlyappear in the MicrosoftML documentation. If it is added in a future update, we can uncomment this text.

, or in the `rxNeuralNetwork()` function in [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml/microsoftml). 

-->

本文介绍开发自定义神经网络所需的基本概念： 

* 神经网络要求以及如何定义主要组件
* Net# 规范语言的语法和关键字
* 使用 Net# 创建的自定义神经网络的示例 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>神经网络基础知识
神经网络结构包括***层***中组织的***节点***，以及节点之间的加权***连接***（或***边缘***）。 连接为定向拓扑，每个连接具有一个***源***节点和一个***目标***节点。  

每个***可训练层***（隐藏或输出层）具有一个或多个***连接捆绑***。 连接捆绑包括一个源层和该源层中的连接规范。 给定捆绑中的所有连接共享同一***源层***和同一***目标层***。 在 Net # 中，连接捆绑将视为属于捆绑的目标层。  

Net # 支持各种类型的连接捆绑，可自定义映射到隐藏层和映射到输出的输入方式。   

默认或标准捆绑是一个**完整捆绑**，其中源层中的每个节点都连接到目标层中的每个节点。  

此外，Net# 支持以下四种高级连接捆绑：  

* **筛选捆绑**。 用户可通过使用源层节点和目标层节点的位置来定义一个谓词。 每当谓词为 Ture，节点即连接。
* **卷积捆绑**。 用户可在源层中定义节点的小范围邻域。 目标层中的每个节点连接到源层中节点的一个邻域。
* **池捆绑**和**响应规范化**捆绑。 这些与卷积捆绑类似，用户可在其中定义源层中小范围的邻域。 不同之处在于这些捆绑中边缘的权重不可训练。 相反，为源节点值应用预定义的函数可确定目标节点值。  

使用 Net# 定义神经网络的结构使定义复杂结构（如深层神经网络或任意维度的卷积）变得可能，这些复杂结构被认为可提高对数据的学习，如映像、音频或视频。  

## <a name="supported-customizations"></a>支持的自定义项
在 Azure 机器学习中创建的神经网络模型的体系结构可通过使用 Net# 广泛自定义。 可以：  

* 创建隐藏层并控制每层的节点数。
* 指定如何相互连接层。
* 定义特殊的连接结构，如卷积和权重共享捆绑。
* 指定不同的激活函数。  

有关规范语言语法的详细信息，请参阅 [结构规范](#Structure-specifications)。  

有关为某些常见机器学习任务定义神经网络的示例（从单一到复杂），请参阅[示例](#Examples-of-Net#-usage)。  

## <a name="general-requirements"></a>一般要求
* 必须正好是一个输出层，至少一个输入层，以及零个或多个隐藏层。 
* 每层都有固定节点数，在任意维度的举行数组中按概念排列。 
* 输入层没有关联的训练参数，并表示实例数据进入网络的点。 
* 可训练层（隐藏和输出层）具有关联的培训参数，称为权重和偏差。 
* 源和目标节点必须处于不同的层。 
* 连接必须是非循环的；换句话说，不能有导回初始源节点的连接链。
* 输出层不能是连接捆绑的源层。  

## <a name="structure-specifications"></a>结构规范
神经网络结构规范由三部分组成：**常数声明**、**层声明**、**连接声明**。 还有另一可选部分：**共享声明**。 可以按任意顺序指定这些部分。  

## <a name="constant-declaration"></a>常数声明
常数声明是可选的。 它提供了一种方法来定义神经网络定义中其他位置使用的值。 声明语句包含标识符（后跟一个等号和值表达式）。   

例如，下面的语句定义一个常量 **x**：  

    Const X = 28;  

要同时定义两个或多个常量，可将标识符名称和值括在括号内，并使用分号隔开。 例如：  

    Const { X = 28; Y = 4; }  

每个赋值表达式的右侧可以是整数、实数、 布尔值（True 或 False）或数学表达式。 例如：  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>层声明
层声明是必需的。 它定义层的大小和源，包括其连接捆绑和属性。 声明语句以层（输入、隐藏或输出）名称为开头，后跟层的维度（正整数的元组）。 例如：  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

* 维度的乘积是层中节点数。 在此示例中，有两个维度 [5,20]，这意味着层中有 100 个节点。
* 层可按任意顺序进行声明，但有一个例外：如果定义了多个输入层，那么声明的顺序必须与输入数据中的功能顺序匹配。  

若要指定自动确定层中节点数，可使用 **auto** 关键字。 **auto** 关键字具有不同的效果，具体取决于层：  

* 在输入层声明中，节点数是输入数据中的功能数。
* 在隐藏层声明中，节点数是**隐藏节点数**的参数值指定的数。 
* 在输出层声明中，双类分类的节点数是 2，回归的节点数是 1，最多分类的节点数与输出节点数相等。   

例如，以下网络定义可自动确定所有层的大小：  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


可训练层（隐藏或输出层）的层声明可选择包括输出函数（也称为激活函数），这会分类模型默认为 **sigmoid**，回归模型默认为 **linear**。 （即使使用默认值，为了清楚起见，也可以显式声明激活函数。）

支持以下输出函数：  

* sigmoid
* linear
* softmax
* rlinear
* square
* sqrt
* srlinear
* abs
* tanh 
* brlinear  

例如，下面的声明使用 **softmax** 函数：  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>连接声明
定义可训练层之后，必须声明已定义的层中的连接。 连接捆绑以关键词 **from** 为开头，后跟捆绑的源层名称以及要创建的连接捆绑的种类。   

当前支持五种类型的连接捆绑：  

* **完整**捆绑，由关键字 **all** 指示
* **筛选**捆绑，由关键字 **where** 指示，后跟谓词表达式
* **卷积捆绑**，由关键字 **convolve** 指示，后跟卷积属性
* **池**捆绑，由关键字 **max pool** 或 **mean pool** 指示
* **响应规范**捆绑，由关键字 **response norm** 指示      

## <a name="full-bundles"></a>完整捆绑
完整捆绑包括源层中每个节点到目标层中每个节点的连接。 这是默认网络连接类型。  

## <a name="filtered-bundles"></a>筛选捆绑
筛选连接捆绑规范包含一个谓词，在语法表达上，更类似 C# lambda 表达式。 下面的示例定义两个筛选捆绑：  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

* 在 *ByRow* 的谓词中，**s** 是输入层 *Pixels* 节点的矩形数组中表示索引的参数，**d** 是隐藏层 *ByRow* 节点的数组中表示索引的参数。 **s** 和 **d** 的类型是长度为 2 的整数 的元组。 从概念上讲，**s** 包括了 *0 <= s[0] < 10* 和 *0 <= s[1] < 20* 的所有整数对，而 **d** 包括了 *0 <= d[0] < 10* 和 *0 <= d[1] < 12* 的所有整数对。 
* 在谓词表达式的右侧端上有一个条件。 在此示例中，对于 **s** 和 **d** 的每个值，该条件为 True，从源层节点到目标层节点有一个边缘。 因此，在所有 s[0] 等于 d[0] 的情况下，此筛选表达式指示捆绑包括从由 **s** 定义的节点到由 **d** 定义的节点的连接。  

或者，可以为筛选的捆绑指定一组权重。 **Weights** 属性的值必须是浮点值的元组，其长度与捆绑定义的连接数匹配。 默认情况下，权重是随机生成的。  

权重值按照目标节点索引进行分组。 也就是说，在源索引顺序中，如果第一个目标节点连接到 K 源节点，则 **Weights** 元组的第一个 *K* 元素为第一个目标节点的权重。 这同样适用于其他目标节点。  

可直接将权重指定为常量值。 例如，如果以前了解权重，则可以使用此语法将其指定为常量：

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>卷积捆绑
如果训练数据具有同类结构，则通常使用卷积捆绑来了解数据的高级功能。 例如，映像、音频或视频数据中的空间或临时维数可以很统一。  

卷积捆绑使用通过维度滑动的矩形**内核**。 实质上，每个内核定义一组在本地邻域中应用的权重，称为**内核应用程序**。 每个内核应用程序对应一个源层中的节点，称为**内核节点**。 内核的权重在多个连接中共享。 在卷积捆绑中，每个内核都是矩形状的，并且所有内核应用程序都具有相同大小。  

卷积捆绑支持一下属性：

**InputShape** 可定义用于此卷积捆绑的源层维数。 值必须是正整数的元组。 整数的乘积必须等于源层中的节点数，否则，不需要与源层声明的维数匹配。 此元组的长度会成为卷积捆绑的**实参数量**值。 （通常实参数量表示函数可采用的参数或操作的数量。）  

若要定义内核的形状和位置，可使用属性 **KernelShape**、**Stride**、**Padding**、**LowerPad** 或 **UpperPad**：   

* **KernelShape**：（必需）为卷积捆绑定义每个内核的维数。 值必须是正整数的元组，其长度等于捆绑的实参数量。 此元组的每个组件必须不超过 **InputShape** 的相应组件。 
* **Stride**：（可选）定义卷积的滑动步大小（每个维度的每步大小），即中央节点之间的距离。 值必须是正整数的元组，其长度为捆绑的实参数量。 此元组的每个组件必须不超过 **KernelShape** 的相应组件。 默认值为一个元组，其所有组件都等于 1。 
* **Sharing**：（可选）定义卷积的每个维度的权重共享。 值可以是单个布尔值或布尔值的元组（其长度为捆绑的实参数量）。 单个布尔值扩展为正确长度的元组，所有组件都等于指定值。 默认值为包含所有 True 值的元组。 
* **MapCount**：（可选）为卷积捆绑定义功能映射数。 值可以是单个正整数或正整数的元组（其长度为捆绑的实参数量）。 单个整数值扩展为正确长度的元组，第一个组件等于指定值，其他所有组件等于 1. 默认值为 1。 功能映射的总数是元组组件的乘积。 组件之间的总数的因数分解可确定目标节点中功能映射值的分组方式。 
* **Weights**：（可选）定义捆绑的初始权重。 值必须是浮点值的元组，其长度为内核数乘以每个内核的权重数，会在本文后面部分中定义。 默认权重是随机生成的。  

有两组控制填充的属性，这两个属性相互排斥：

* **Padding**：（可选）确定是否应该使用**默认填充方案**来填充输入。 值可以是单个布尔值或布尔值的元组（其长度为捆绑的实参数量）。 单个布尔值扩展为正确长度的元组，所有组件都等于指定值。 如果维度值为 True，则使用零值单元格将源按逻辑填充到维度以支持其他内核应用程序，从而使该维度中第一个和最后一个内核的中央节点称为源层的维度中的第一个和最后一个节点。 因此，每个维度中的“虚拟”节点数将自动确定，以使 *(InputShape[d] - 1) / Stride[d] + 1* 内核完全符合填充的源层。 如果维度值为 False，则将定义内核，使留出的每个端上的节点数都相同（最大差值为 1）。 此属性的默认值为一个元组，其所有组件都等于 False。
* **UpperPad** 和 **LowerPad**：（可选）对大量要使用的填充提供更好的控制。 **重要提示：**当且仅当***没有***定义上述的 **Padding** 属性时，才能定义这些属性。 值必须是正整数值的元组，其长度为绑定的实参数量。 指定这些参数后，“虚拟”节点将添加到输入层的每个维度的上下两端。 每个维度的上下两端添加的节点数分别由 **LowerPad**[i] 和 **UpperPad**[i] 确定。 若要确保内核只对应“真实”节点而不是“虚拟”节点，则必须符合以下条件：
  * **LowerPad** 的每个组件必须准确小于 KernelShape[d]/2。 
  * **UpperPad** 的每个组件必须准确小于 KernelShape[d]/2。 
  * 这些属性的默认值为一个元组，其所有组件都等于 0。 

设置 **Padding** = true 允许尽可能多的填充，使内核的“中心”保持在“真实”输入内。 这会对数学做出一些更改，以便计算输出大小。 通常情况下，按照 *D = (I - K) / S + 1* 计算输出大小 *D*，其中 *I* 表示输入大小，*K* 表示内核大小，*S* 表示 stride，*/* 表示整数除法（向零舍入）。 如果设置 UpperPad = [1, 1]，那么输入大小 *I* 实际上是 29，因而 *D = (29 - 5) / 2 + 1 = 13*。 但是，如果 **Padding** = true，则 *I* 实际增长为 *K - 1*；因此 *D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14*。 通过为 **UpperPad** 和 **LowerPad** 指定值，相比只设置 **Padding** = true，可以更好地控制填充。

有关卷积网络及其应用程序的详细信息，请参阅这些文章：  

* [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
* [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
* [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>池捆绑
**池捆绑**适用于类似卷积连接的几何，但是它使用源节点的预定义函数来派生目标节点值。 因此，池捆绑不具有可训练状态（权重或偏差）。 池捆绑支持所有卷积属性，除了 **Sharing**、**MapCount** 和 **Weights**。  

通常情况下，按相邻池单位汇总的内核不会重叠。 如果在每个维度中 Stride[d] 等于KernelShape[d] ，那么获取的层为传统本地池层，其广泛应用于卷积神经网络。 每个目标节点将计算源层中其内核的最大活动数或平均值。  

以下示例对池捆绑进行了说明： 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

* 捆绑的实参数量为 3（元组 **InputShape**、**KernelShape** 和 **Stride** 的长度）。 
* 源层中的节点数为 5 * 24 * 24 = 2880。 
* 这是传统本地池层，因为 **KernelShape** 和 **Stride** 相等。 
* 目标层中的节点数为 5 * 12 * 12 = 1440。  

有关池层的详细信息，请参阅这些文章：  

* [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)（第 3、4 节）
* [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
* [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>响应规范化捆绑
**响应规范化**是本地规范化方案，由 Geoffrey Hinton 等人在文章 [ImageNet Classiﬁcation with Deep Convolutional Neural Networks](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)（深层卷积神经网络的 ImageNet 分类）中首次引入。 响应规范化用于避免神经网络中的通用化。 一个神经元在一个非常高的激活级别中激发时，本地响应规范化层将抑制周围神经元的激活级别。 这会通过使用三个参数（***α***、***β*** 和 ***k***）和一个卷积结构（或邻域形状）来完成。 目标层 ***y*** 中的每个神经元对应于源层中神经元 ***x***。 ***y*** 的激活级别由以下公式指定，其中 ***f*** 是神经元的激活级别，***Nx*** 是内核（或包含 ***x*** 邻域中神经元的集），由以下卷积结构定义：  

![][1]  

响应规范化捆绑支持所有卷积属性，除了 **Sharing**、**MapCount** 和 **Weights**。  

* 如果内核包含与 ***x*** 相同的映射中的神经元，则规范化方案称为**相同映射规范化**。 若要定义相同映射规范化，那么 **InputShape** 中的第一个坐标必须具有值 1.
* 如果内核包含与 ***x*** 相同的空间位置中的神经元，但是神经元位于其他映射中，则规范化方案称为**跨映射规范化**。 这种类型的响应规范化可实现一种横向抑制，其灵感来源于从真实神经元中发现的类型，可创建不同映射上计算的神经元输出之间的大激活级别的竞争。 若要定义跨映射规范化，第一个坐标必须是大于 1 且不大于映射数的正整数，其他坐标则必须具有值 1.  

因为响应规范化捆绑应用源节点值的预定义函数以确定目标节点值，所以它们不具有可训练状态（权重或偏差）。   

**警报**：目标层中的节点对应于是内核的中央节点的神经元。 例如，如果 KernelShape[d] 为奇数，则 *KernelShape[d]/2* 与中央内核节点相对应。 如果 *KernelShape[d]* 为偶数，则中心节点位于 *KernelShape[d]/2 - 1*。 因此，如果 **Padding**[d] 为 False，那么第一个和最后一个 *KernelShape[d]/2* 节点在目标层中没有对应节点。 要避免这种情况，可将 **Padding** 定义为 [true, true, …, true]。  

除了前面所述的四个属性，响应规范化捆绑还支持以下属性：  

* **Alpha**：（必需）指定一个与前面的公式中 ***α*** 对应的浮点值。 
* **Beta**：（必需）指定一个与前面的公式中 ***β*** 对应的浮点值。 
* **Offset**：（可选）指定一个与前面的公式中 ***k*** 对应的浮点值。 默认为 1。  

下面的示例使用这些属性定义回应规范化捆绑：  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

* 源层包括五个映射，每个具有一个 12x12 维度，总计 1440 个节点。 
* 值 **KernelShape** 指示这是一个相同的映射规范化层，其中邻域为一个 3x3 矩形。 
* **Padding** 的默认值为 False，因此目标层的每个维度中只有 10 个节点。 要包括一个与源层中每个节点对应的目标层中的节点，可添加 Padding = [true, true, true]；然后将 RN1 的大小更改为 [5, 12, 12]。  

## <a name="share-declaration"></a>共享声明
Net # 可选择支持定义具有共享权重的多个捆绑。 如果任意两个捆绑的结构相同，则其权重可以共享。 以下语法可定义具有共享权重的捆绑：  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }

    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name

    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec

    bundle-spec:
       layer-name    =>     layer-name

    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec

    bias-spec:
        1    =>    layer-name

    layer-name:
        identifier  

例如，以下共享声明指定层名称，指示应共享两个权重和偏差：  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

* 输入功能划分为两个相等大小的输入层。 
* 隐藏层则计算两个输入层上的更高级别的功能。 
* 共享声明指定 *H1* 和 *H2* 必须从其各自的输入中以相同的方式进行计算。  

或者，可以使用两个单独的共享声明来指定，如下所示：  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

只有在层包含单个捆绑时，才可以使用缩写形式。 一般情况下，只有在相关结构相同时（即具有相同大小、相同卷积几何等），才能共享。  

## <a name="examples-of-net-usage"></a>Net # 用法的示例
本部分提供了一些示例，可了解如何使用 Net# 添加隐藏层，定义隐藏层与其它层交互的方式，以及生成卷积网络。   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>定义一个简单的自定义神经网络：“Hello World”示例
这个简单的示例演示了如何创建具有单个隐藏层的神经网络模型。  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

该示例阐释了一些基本的命令，如下所示：  

* 第一行定义输入层（名为 *Data*）。 使用 **auto** 关键字时，神经网络会自动包括输入示例中的所有功能列。 
* 第二行创建隐藏层。 名称 *H* 将分配到具有 200 个节点的隐藏层。 这一层完全连接到输入层。
* 第三行定义输出层（名为 *O*），其中包含 10 个输出节点。 如果神经网络用于分类，则每个类有一个输出节点。 关键字 **sigmoid** 指示输出函数将应用于输出层。   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>定义多个隐藏层：计算机影像示例
下面的示例将演示如何使用多个自定义隐藏层，定义稍微复杂一些的神经网络。  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];

    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }

    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

此示例说明了神经网络规范语言的多个功能：  

* 此结构中包含两个输入层：*Pixels* 和 *MetaData*。
* *Pixels* 层是两个连接捆绑的源层（目标层为 *ByRow* 和 *ByCol*）。
* 层 *Gather* 和 *Result* 是多个连接捆绑中的目标层。
* 输出层 *Result* 是两个连接捆绑中的目标层；一个以第二级隐藏 (Gather) 作为目标层，另一个以输入层 (MetaData) 作为目标层。
* 隐藏层 *ByRow* 和 *ByCol*，将通过使用谓词表达式来指定筛选的连接。 更确切地说，在 [x, y] 的 *ByRow* 中的节点连接到 *Pixels* 中的节点（第一个索引坐标等于节点的第一个坐标 x）。 同样，[x, y] 处 ByCol 中的节点连接到 _Pixels 中的节点（第二个索引坐标位于节点的第二个坐标 y 内）。  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>为多类分类定义卷积网络：数字识别示例
以下网络的定义旨在识别数字，并说明了自定义神经网络的一些高级技术。  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


* 此结构具有单个输入层 *Image*。
* 关键字 **convolve** 指示名为 *Conv1* 和 *Conv2* 的层是卷积层。 每个这些层声明都后跟一个卷积属性列表。
* Net 具有第三个隐藏层 *Hid3*，其与第二个隐藏层 *Conv2* 完全连接。
* 输出层 *Digit* 仅与第三个隐藏层 *Hid3* 连接。 关键字 **all** 指示输出层与 *Hid3* 完全连接。
* 卷积的实参数量为 3（元组 **InputShape**、**KernelShape**、**Stride** 和 **Sharing** 的长度）。 
* 每个内核的权重数是 *1 + **KernelShape**\[0] * **KernelShape**\[1] * **KernelShape**\[2] = 1 + 1 * 5 * 5 = 26。或 26 * 50 = 1300*。
* 可以计算每个隐藏层中的节点，如下所示：
  * **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5。
  * **NodeCount**\[1] = (13 - 5) / 2 + 1 = 5。 
  * **NodeCount**\[2] = (13 - 5) / 2 + 1 = 5。 
* 可通过使用层的声明维数 [50, 5, 5] 计算节点总数，如下所示：***MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5*
* 因为仅对于 *d == 0*，**Sharing**[d] 为 False，所以内核数为 ***MapCount** * **NodeCount**\[0] = 10 * 5 = 50*。 

## <a name="acknowledgements"></a>致谢
用于自定义神经网络体系结构的 Net# 语言由 Microsoft 的 Shon Katzenberger（架构师，机器学习）和 Alexey Kamenev（软件工程师，Microsoft Research）开发。 在内部，其用于机器学习项目和应用程序，其范围包括从映像检测到文本分析。 有关详细信息，请参阅 [Neural Nets in Azure ML - Introduction to Net#](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)（Azure ML 中的神经网络 - Net# 简介）

[1]:./media/azure-ml-netsharp-reference-guide/formula_large.gif


