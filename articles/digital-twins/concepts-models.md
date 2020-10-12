---
title: 自定义模式
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生如何使用用户定义的模型来描述您的环境中的实体。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7da19ddd96c15ff5688d6e153d1859ed8c11ec8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616544"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>了解 Azure 数字孪生中的克隆模型

Azure 数字孪生的一项重要特征是能够定义自己的词汇，并以业务的自定义条款构建克隆图形。 此功能是通过用户定义 **模型**提供的。 您可以将模型视为您的全球说明中的名词。 

模型类似于面向对象的编程语言中的 **类** ，它定义了真实工作环境中一个特定概念的数据形状。 模型具有 (（如 *房间* 或 *温度传感器*) ）的名称，并且包含属性、遥测/事件和命令等元素，这些元素描述了环境中此类型实体的作用。 稍后，您将使用这些模型创建代表满足此类型说明的特定实体的 [**数字孪生**](concepts-twins-graph.md) 。

使用基于 JSON LD 的 **数字克隆定义语言 (DTDL) **来编写模型。  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>数字克隆定义语言 (DTDL) 用于编写模型

Azure 数字孪生的模型是使用数字孪生定义语言 (DTDL) 定义的。 DTDL 基于 JSON-LD，是独立于编程语言的。 DTDL 不是 Azure 数字孪生独有的，但也用于表示 [iot 即插即用](../iot-pnp/overview-iot-plug-and-play.md)等其他 IoT 服务中的设备数据。 

Azure 数字孪生使用 **DTDL _版本 2_**。 有关此版本的 DTDL 的详细信息，请参阅 GitHub 中的规范文档： [*数字孪生定义语言 (DTDL) 版本 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 在 Azure 数字孪生中使用 DTDL _版本 1_ 现已弃用。

> [!NOTE] 
> 并非所有使用 DTDL 的服务都实现 DTDL 的完全相同的功能。 例如，IoT 即插即用不使用适用于图形的 DTDL 功能，而 Azure 数字孪生目前不实现 DTDL 命令。
>
> 有关特定于 Azure 数字孪生的 DTDL 功能的详细信息，请参阅本文后面的 [Azure 数字孪生 DTDL 实现细节](#azure-digital-twins-dtdl-implementation-specifics)部分。

## <a name="elements-of-a-model"></a>模型的元素

在模型定义中，顶级代码项是一个 **接口**。 这将封装整个模型，并在接口中定义模型的其余部分。 

DTDL 模型接口可能包含以下每个字段中的零个、一个或多个字段：
* **属性** -属性是表示实体的状态的数据字段 (类似于许多面向对象的编程语言) 中的属性。 属性具有后备存储，可随时读取。
* **遥测** -遥测字段表示度量值或事件，通常用于描述设备传感器读数。 与属性不同，遥测数据不会存储在数字克隆上;这是一系列需要在发生时进行处理的时间限制的数据事件。 有关属性和遥测之间的差异的详细信息，请参阅下面的 " [*属性与遥测*](#properties-vs-telemetry) " 部分。
* 如果需要，**组件**组件允许将模型接口构建为其他接口的程序集。 组件的一个示例是 (的 *frontCamera* 接口，另一个组件接口 *backCamera*) 用于为 *手机*定义模型。 必须先为 *frontCamera* 定义一个接口，就像它是其自己的模型一样，然后在定义 *手机*时可以引用它。

    使用组件来描述作为解决方案不可分割但不需要单独标识的内容，而无需单独在克隆图形中创建、删除或重新排列。 如果希望实体在 existences 图中具有独立的，则将其表示为不同模型的单独数字孪生，通过 *关系* 进行连接 (参阅下一项目符号) 。
    
    >[!TIP] 
    >组件还可用于组织，以对模型接口中的相关属性集进行分组。 在这种情况下，你可以将每个组件视为命名空间，或将 "文件夹" 视为接口。
* **关系** -关系使你可以表示数字克隆如何与其他数字孪生一起使用。 关系可以表示不同的语义含义，如 *contains* ( "楼层包含空间" ) ， *冷却* ( "hvac 冷却房间" ) ， *isBilledTo* ( "为用户向用户计费" ) 等。关系允许解决方案提供关联实体的关系图。

> [!NOTE]
> [DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)还定义命令，这些**命令**可以在数字克隆 (（如 reset 命令）或用于切换风扇) 的命令。 但是， *Azure 数字孪生目前不支持命令。*

### <a name="properties-vs-telemetry"></a>属性与遥测

下面是有关在 Azure 数字孪生中区分 DTDL **属性** 和 **遥测** 字段的一些附加指导。

Azure 数字孪生模型的属性和遥测的区别如下：
* **属性** 应具有后备存储。 这意味着您可以随时读取属性并检索其值。 如果该属性是可写的，则还可以在属性中存储值。  
* **遥测** 更像是事件流;这是一组具有短 lifespans 的数据消息。 如果不设置侦听事件和发生事件时要执行的操作，则不会在以后进行事件跟踪。 你不能返回它并稍后阅读。 
  - 在 c # 术语中，遥测类似于 c # 事件。 
  - 在 IoT 术语中，遥测通常是设备发送的一种度量。

**遥测** 数据通常与 IoT 设备一起使用，因为许多设备不能或不感兴趣地存储所生成的度量值。 它们只是将它们作为 "遥测" 事件流发送。 在这种情况下，无法随时针对遥测字段的最新值查询设备。 相反，你需要从设备中侦听消息，并在消息到达时执行操作。 

因此，在 Azure 数字孪生中设计模型时，可能会在大多数情况下使用 **属性** 来为孪生建模。 这使你可以使用后备存储和读取和查询数据字段的功能。

遥测和属性通常协同工作以处理来自设备的数据入口。 由于所有入口都是通过 [api](how-to-use-apis-sdks.md)进行的，因此你通常会使用入口函数从设备读取遥测数据或属性事件，并在 ADT 中将属性设置为 "响应"。 

还可以从 Azure 数字孪生 API 发布遥测事件。 与其他遥测一样，这是一个生存期较短的事件，需要侦听器才能处理。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure 数字孪生 DTDL 实现细节

要使 DTDL 模型与 Azure 数字孪生兼容，必须满足这些要求。

* 模型中的所有顶级 DTDL 元素必须属于类型 *接口*。 这是因为 Azure 数字孪生模型 Api 可以接收表示接口或接口数组的 JSON 对象。 因此，不允许在顶级使用其他 DTDL 元素类型。
* DTDL for Azure 数字孪生不能定义任何 *命令*。
* Azure 数字孪生只允许使用单个级别的组件嵌套。 这意味着用作组件的接口不能有任何组件本身。 
* 无法在其他 DTDL 接口内以内联方式定义接口;它们必须定义为具有自己的 Id 的单独顶级实体。 然后，当另一个接口要将该接口包含为组件或继承时，它可以引用其 ID。

Azure 数字孪生也不会 `writable` 对属性或关系观察属性。 尽管可以按 DTDL 规范设置此值，但 Azure 数字孪生不会使用此值。 相反，它们始终被视为对 Azure 数字孪生服务具有常规写入权限的外部客户端可以写入。

## <a name="example-model-code"></a>示例模型代码

可以在任何文本编辑器中编写克隆类型模型。 DTDL 语言遵循 JSON 语法，因此应存储扩展名为 *JSON*的模型。 使用 JSON 扩展会使许多编程文本编辑器为你的 DTDL 文档提供基本语法检查和突出显示。 还有一个可用于[Visual Studio Code](https://code.visualstudio.com/)的[DTDL 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。

本部分包含以 DTDL 接口形式编写的典型模型的示例。 此模型描述 **行星**，每个行星都具有名称、质量和温度。
 
请考虑行星还可能会与 **卫星一起交互，其中可能** 包含 **craters**。 在下面的示例中，该 `Planet` 模型通过引用两个外部模型（和）来表示与这些其他实体的连接 `Moon` `Crater` 。 下面的示例代码中也定义了这些模型，但它们非常简单，因此不会降低主要的 `Planet` 示例。

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

| 字段 | 说明 |
| --- | --- |
| `@id` | 模型的标识符。 必须采用格式 `dtmi:<domain>:<unique model identifier>;<model version number>` 。 |
| `@type` | 标识所描述的信息的类型。 对于接口，类型为 *interface*。 |
| `@context` | 设置 JSON 文档的 [上下文](https://niem.github.io/json/reference/json-ld/context/) 。 模型应使用 `dtmi:dtdl:context;2` 。 |
| `displayName` | 可有可无允许您为模型指定友好名称（如果需要）。 |
| `contents` | 所有剩余的接口数据都放置在此处，作为特性定义的数组。 每个属性都必须 `@type` 提供 * (属性*、 *遥测*、 *命令*、 *关系*或 *组件*) ，以标识它所描述的接口信息的排序，然后是定义实际 (属性的一组属性（例如） `name` ，并 `schema` 定义 *属性*) 。 |

> [!NOTE]
> 请注意，在此示例中 (*Crater* 的组件接口) 与 (*行星*) 使用该接口的接口在同一阵列中定义。 必须以这种方式在 API 调用中定义组件，才能找到接口。

### <a name="possible-schemas"></a>可能的架构

根据 DTDL， *属性* 和 *遥测* 属性的架构可以是标准基元类型（ `integer` 、 `double` 、和），也可以是 `string` `Boolean` 其他类型（如 `DateTime` 和） `Duration` 。 

除了基元类型外， *属性* 和 *遥测* 字段还可以具有以下复杂类型：
* `Object`
* `Map`
* `Enum`

*遥测* 字段也支持 `Array` 。

### <a name="model-inheritance"></a>模型继承

有时，你可能需要进一步专用化模型。 例如，有一个通用的模型 *房间*，并使用专用变量 *ConferenceRoom* 和 *健身房*可能会很有用。 为了表达专用化，DTDL 支持继承：接口可以从一个或多个其他接口继承。 

下面的示例将 imagines 中的 *行星* 模型重新调整为更大 *CelestialBody* 模型的子类型。 首先定义 "parent" 模型，然后使用该字段在其上构建 "child" 模型 `extends` 。

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

在此示例中， *CelestialBody* 向 *行星*提供名称、质量和温度。 `extends`部分是接口名称或接口名称的数组， (允许扩展接口从多个父模型继承（如果需要）) 。

应用继承后，扩展接口将公开整个继承链中的所有属性。

扩展接口无法更改父接口的任何定义;它只能添加到其中。 即使将功能定义为同一) ，它也不能重新定义已在任何父接口中定义的功能 (。 例如，如果父接口定义了一个 `double` 属性 *质量*，则扩展接口不能包含 *质量*的声明，即使它也是 `double` 。

## <a name="validating-models"></a>验证模型

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="next-steps"></a>后续步骤

请参阅如何通过 DigitalTwinsModels Api 管理模型：
* [*操作说明：管理自定义模型*](how-to-manage-model.md)

或者，了解如何基于模型创建数字孪生：
* [*概念：数字孪生和双子图形*](concepts-twins-graph.md)

