---
title: 自定义模式
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生如何使用用户定义的模型来描述您的环境中的实体。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 56ebb32e2d1c2a9bab9592da63e1ada7130bb7ff
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131627"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>了解 Azure 数字孪生中的克隆模型

Azure 数字孪生的一项重要特征是能够定义自己的词汇，并以业务的自定义条款构建克隆图形。 此功能是通过用户定义**模型**提供的。 您可以将模型视为您的全球说明中的名词。 

模型类似于面向对象的编程语言中的**类**，它定义了真实工作环境中一个特定概念的数据形状。 模型具有名称（如*房间*或*温度传感器*），并且包含属性、遥测/事件和命令等元素，这些元素用于描述此类实体在环境中的作用。 稍后，您将使用这些模型创建代表满足此类型说明的特定实体的[**数字孪生**](concepts-twins-graph.md)。

模型是使用基于 JSON 的**数字克隆定义语言（DTDL）** 编写的。  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>用于写入模型的数字克隆定义语言（DTDL）

Azure 数字孪生的模型是使用数字孪生定义语言（DTDL）定义的。 DTDL 基于 JSON-LD，是独立于编程语言的。 DTDL 不是 Azure 数字孪生独有的，但也用于表示[iot 即插即用](../iot-pnp/overview-iot-plug-and-play.md)等其他 IoT 服务中的设备数据。 

Azure 数字孪生使用 DTDL*版本 2*。 有关此版本的 DTDL 的详细信息，请参阅 GitHub 中的规范文档：[*数字孪生定义语言（DTDL）-版本 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

> [!TIP] 
> 并非所有使用 DTDL 的服务都实现 DTDL 的完全相同的功能。 例如，IoT 即插即用不使用适用于图形的 DTDL 功能，而 Azure 数字孪生目前不实现 DTDL 命令。 有关特定于 Azure 数字孪生的 DTDL 功能的详细信息，请参阅本文后面的[Azure 数字孪生 DTDL 实现细节](#azure-digital-twins-dtdl-implementation-specifics)部分。

## <a name="elements-of-a-model"></a>模型的元素

在模型定义中，顶级代码项是一个**接口**。 这将封装整个模型，并在接口中定义模型的其余部分。 

DTDL 模型接口可能包含以下每个字段中的零个、一个或多个字段：
* **属性**-属性是表示实体状态的数据字段（类似于许多面向对象的编程语言中的属性）。 与遥测（这是时间绑定的数据事件）不同，属性具有后备存储并且可随时读取。
* **遥测**-遥测字段表示度量值或事件，通常用于描述设备传感器读数。 遥测数据不会存储在数字克隆上;它更像是准备发送到某个位置的数据事件流。 
* 如果需要，**组件**组件允许将模型接口构建为其他接口的程序集。 组件的一个示例是在为*手机*定义模型时使用的*frontCamera*接口（以及另一个组件接口*backCamera*）。 必须先为*frontCamera*定义一个接口，就像它是其自己的模型一样，然后在定义*手机*时可以引用它。

    使用组件来描述作为解决方案不可分割但不需要单独标识的内容，而无需单独在克隆图形中创建、删除或重新排列。 如果希望实体在 existences 图中具有独立的，则将其表示为不同模型的单独数字孪生，并按*关系*连接（请参阅下一项目符号）。
    
    >[!TIP] 
    >组件还可用于组织，以对模型接口中的相关属性集进行分组。 在这种情况下，你可以将每个组件视为命名空间，或将 "文件夹" 视为接口。
* **关系**-关系使你可以表示数字克隆如何与其他数字孪生一起使用。 关系可以表示不同的语义含义，如*contains* （"楼层包含空间"）、*冷却*（"hvac 冷却房间"）、 *isBilledTo* （"对用户计费压缩机"）等。关系允许解决方案提供关联实体的关系图。

> [!NOTE]
> DTDL 规范还定义了一些**命令**，这些命令可以在数字克隆（如重置命令，或用于切换风扇的命令）上执行。 但是， *Azure 数字孪生目前不支持命令。*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 数字孪生 DTDL 实现细节

要使 DTDL 模型与 Azure 数字孪生兼容，必须满足这些要求。

* 模型中的所有顶级 DTDL 元素必须属于类型*接口*。 这是因为 Azure 数字孪生模型 Api 可以接收表示接口或接口数组的 JSON 对象。 因此，不允许在顶级使用其他 DTDL 元素类型。
* DTDL for Azure 数字孪生不能定义任何*命令*。
* Azure 数字孪生只允许使用单个级别的组件嵌套。 这意味着用作组件的接口不能有任何组件本身。 
* 无法在其他 DTDL 接口内以内联方式定义接口;它们必须定义为具有自己的 Id 的单独顶级实体。 然后，当另一个接口要将该接口包含为组件或继承时，它可以引用其 ID。

## <a name="example-model-code"></a>示例模型代码

可以在任何文本编辑器中编写克隆类型模型。 DTDL 语言遵循 JSON 语法，因此应存储扩展名为*JSON*的模型。 使用 JSON 扩展会使许多编程文本编辑器为你的 DTDL 文档提供基本语法检查和突出显示。 还有一个可用于[Visual Studio Code](https://code.visualstudio.com/)的[DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。

本部分包含以 DTDL 接口形式编写的典型模型的示例。 此模型描述**行星**，每个行星都具有名称、质量和温度。
 
请考虑行星还可能会与**卫星一起交互，其中可能**包含**craters**。 在下面的示例中，该 `Planet` 模型通过引用两个外部模型（和）来表示与这些其他实体的连接 `Moon` `Crater` 。 下面的示例代码中也定义了这些模型，但它们非常简单，因此不会降低主要的 `Planet` 示例。

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

模型的字段为：

| 字段 | 描述 |
| --- | --- |
| `@id` | 模型的标识符。 必须采用格式 `dtmi:<domain>:<unique model identifier>;<model version number>` 。 |
| `@type` | 标识所描述的信息的类型。 对于接口，类型为*interface*。 |
| `@context` | 设置 JSON 文档的[上下文](https://niem.github.io/json/reference/json-ld/context/)。 模型应使用 `dtmi:dtdl:context;2` 。 |
| `displayName` | 可有可无允许您为模型指定友好名称（如果需要）。 |
| `contents` | 所有剩余的接口数据都放置在此处，作为特性定义的数组。 每个属性都必须提供一个 `@type` （*属性*、*遥测*、*命令*、*关系*或*组件*）来标识它所描述的接口信息的种类，然后是一组用于定义实际特性的属性（例如， `name` 和 `schema` 用于定义*属性*）。 |

> [!NOTE]
> 请注意，此示例中的组件接口（*Crater* ）是在使用它（*行星*）的接口所在的同一阵列中定义的。 必须以这种方式在 API 调用中定义组件，才能找到接口。

### <a name="possible-schemas"></a>可能的架构

根据 DTDL，*属性*和*遥测*属性的架构可以是标准基元类型（ `integer` 、 `double` 、和），也可以是 `string` `Boolean` 其他类型（如 `DateTime` 和） `Duration` 。 

除了基元类型外，*属性*和*遥测*字段还可以具有以下复杂类型：
* `Object`
* `Map`
* `Enum`

*遥测*字段也支持 `Array` 。

### <a name="model-inheritance"></a>模型继承

有时，你可能需要进一步专用化模型。 例如，有一个通用的模型*房间*，并使用专用变量*ConferenceRoom*和*健身房*可能会很有用。 为了表达专用化，DTDL 支持继承：接口可以从一个或多个其他接口继承。 

下面的示例将 imagines 中的*行星*模型重新调整为更大*CelestialBody*模型的子类型。 首先定义 "parent" 模型，然后使用该字段在其上构建 "child" 模型 `extends` 。

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

在此示例中， *CelestialBody*向*行星*提供名称、质量和温度。 `extends`部分是接口名称或接口名称的数组（如果需要，允许扩展接口从多个父模型继承）。

应用继承后，扩展接口将公开整个继承链中的所有属性。

扩展接口无法更改父接口的任何定义;它只能添加到其中。 它也不能重新定义已在其所有父接口中定义的功能（即使这些功能定义为相同）。 例如，如果父接口定义了一个 `double` 属性*质量*，则扩展接口不能包含*质量*的声明，即使它也是 `double` 。

## <a name="validating-models"></a>验证模型

> [!TIP]
> 建议在将模型上传到 Azure 数字孪生实例之前，先对其进行验证。

有一种与语言无关的示例可用于验证模型文档，以确保 DTDL 是正确的。 其位置为： [**DTDL 验证器示例**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)。

DTDL 验证器示例建立在一个 .NET DTDL 分析器库上，该库在 NuGet 上作为客户端库提供： [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 你还可以直接使用库来设计自己的验证解决方案。 使用分析器库时，请确保使用与 Azure 数字孪生运行的版本兼容的版本。 预览期间，此版本为*3.7.0*。

可以在[*操作方法：分析和验证模型*](how-to-use-parser.md)中了解有关分析器库的详细信息，包括用法示例。

## <a name="next-steps"></a>后续步骤

请参阅如何通过 DigitalTwinsModels Api 管理模型：
* [*操作说明：管理自定义模型*](how-to-manage-model.md)

或者，了解如何基于模型创建数字孪生：
* [*概念：数字孪生和双子图形*](concepts-twins-graph.md)

