---
title: 工作流定义语言架构 - Azure 逻辑应用 | Microsoft Docs
description: 定义基于 Azure 逻辑应用工作流定义架构的工作流
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 42932e6d1727a1444c62f565ae3c48dc178aeb2b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Azure 逻辑应用的工作流定义语言架构

工作流定义包含作为逻辑应用一部分执行的实际逻辑。 此定义包含一个或多个启动逻辑应用的触发器，以及逻辑应用要执行的一个或多个操作。  
  
## <a name="basic-workflow-definition-structure"></a>基本工作流定义结构

下面是工作流定义的基本结构：  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> [工作流管理 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 文档提供了有关如何创建和管理逻辑应用工作流的信息。
  
|元素名称|必选|说明|  
|------------------|--------------|-----------------|  
|$schema|否|指定描述定义语言版本的 JSON 架构文件所在的位置。 从外部引用定义时，需要提供此位置。 下面是本文档位置： <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`|  
|contentVersion|否|指定定义版本。 使用定义部署工作流时，可以使用此值来确保使用适当的定义。|  
|parameters|否|指定用于将数据输入到定义中的参数。 最多可以定义 50 个参数。|  
|触发器|否|指定启动工作流的触发器的信息。 最多可以定义 10 个触发器。|  
|actions|否|指定执行流时要采取的操作。 最多可以定义 250 个操作。|  
|outputs|否|指定有关部署的资源的信息。 最多可以定义 10 个输出。|  
  
## <a name="parameters"></a>parameters

此节指定部署时在工作流定义中使用的所有参数。 只有在此节中声明所有参数后，才能在其他定义节中使用这些参数。  
  
以下示例演示了参数定义的结构：  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|元素名称|必选|说明|  
|------------------|--------------|-----------------|  
|type|是|**类型**：string <p> **声明**：`"parameters": {"parameter1": {"type": "string"}}` <p> **规范**：`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **类型**：securestring <p> **声明**：`"parameters": {"parameter1": {"type": "securestring"}}` <p> **规范**：`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **类型**：int <p> **声明**：`"parameters": {"parameter1": {"type": "int"}}` <p> **规范**：`"parameters": {"parameter1": {"value" : 5}}` <p> **类型**：bool <p> **声明**：`"parameters": {"parameter1": {"type": "bool"}}` <p> **规范**：`"parameters": {"parameter1": { "value": true }}` <p> **类型**：array <p> **声明**：`"parameters": {"parameter1": {"type": "array"}}` <p> **规范**：`"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **类型**：object <p> **声明**：`"parameters": {"parameter1": {"type": "object"}}` <p> **规范**：`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **类型**：secureobject <p> **声明**：`"parameters": {"parameter1": {"type": "object"}}` <p> **规范**：`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **注意：**`securestring` 和 `secureobject` 类型不会在 `GET` 操作中返回。 所有密码、密钥和机密应使用此类型。|  
|defaultValue|否|在创建资源时未指定任何值的情况下指定参数的默认值。|  
|allowedValues|否|指定参数的允许值数组。|  
|metadata|否|指定有关参数的其他信息，例如，Visual Studio 或其他工具使用的可读的说明或设计时数据。|  
  
本示例演示如何在操作的正文节中使用参数：  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 也可以在输出中使用参数。  
  
## <a name="triggers-and-actions"></a>触发器和操作  

触发器和操作指定可参与工作流执行的调用。 有关此节的详细信息，请参阅[工作流操作和触发器](logic-apps-workflow-actions-triggers.md)。
  
## <a name="outputs"></a>Outputs  

输出指定可在运行工作流后返回的信息。 例如，如果想要跟踪每次运行的特定状态或值，可以在运行输出中包含该数据。 数据会显示在该运行的管理 REST API 中，以及 Azure 门户中该运行的管理 UI 中。 还可以将这些输出传送到 PowerBI 等其他外部系统用于创建仪表板。 *不能*使用输出来响应服务 REST API 中的传入请求。 若要使用 `response` 操作类型响应传入请求，请参考以下示例：
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|元素名称|必选|说明|  
|------------------|--------------|-----------------|  
|key1|是|指定输出的密钥标识符。 请将 **key1** 替换为用于标识输出的名称。|  
|值|是|指定输出的值。|  
|type|是|指定已指定的值的类型。 可能的值类型为： <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>表达式  

定义中的 JSON 值可以是文本，也可以是使用定义时要计算的表达式。 例如：  
  
```json
"name": "value"
```

 或  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> 某些表达式从运行时操作获取其值，而这些操作在开始执行时可能不存在。 可以使用**函数**来帮助检索其中的某些值。  
  
表达式可出现在 JSON 字符串值中的任何位置，始终生成另一个 JSON 值。 将某个 JSON 值判定为表达式时，会通过删除 \@ 符号来提取表达式的正文。 如果需要以 \@ 开头的文本字符串，必须使用 \@@ 将它转义。 以下示例演示了如何计算表达式。  
  
|JSON 值|结果|  
|----------------|------------|  
|"parameters"|返回字符“parameters”。|  
|"parameters[1]"|返回字符“parameters[1]”。|  
|"\@@\"|返回包含\“\@\”的、由 1 个字符构成的字符串。|  
|\" \@\"|返回包含“@”的、由 2 个字符构成的字符串。|  
  
如果使用*字符串内插*，表达式还可以显示在 `@{ ... }` 中包装表达式的字符串内。 例如： <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

结果始终是一个字符串，因此，此功能类似于 `concat` 函数。 假设将 `myNumber` 定义为 `42`，将 `myString` 定义为 `sampleString`：  
  
|JSON 值|结果|  
|----------------|------------|  
|"@parameters('myString')"|返回字符串形式的 `sampleString`。|  
|"@{parameters('myString')}"|返回字符串形式的 `sampleString`。|  
|"@parameters('myNumber')"|返回*数字*形式的 `42`。|  
|"@{parameters('myNumber')}"|返回*字符串*形式的 `42`。|  
|"Answer is: @{parameters('myNumber')}"|返回字符串 `Answer is: 42`。|  
|"@concat('Answer is: ', string(parameters('myNumber')))"|返回字符串 `Answer is: 42`|  
|"Answer is: @@{parameters('myNumber')}"|返回字符串 `Answer is: @{parameters('myNumber')}`。|  
  
## <a name="operators"></a>运算符  

运算符是可以在表达式或函数中使用的字符。 
  
|运算符|说明|  
|--------------|-----------------|  
|。|使用点运算符可以引用对象的属性|  
|?|使用问号运算符可以引用未发生运行时错误的对象的 null 属性。 例如，可以使用此表达式来处理 null 触发器输出： <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|只能使用单引号包装字符串文本。 不能在表达式中使用双引号，因为此标点符号与用于包装整个表达式的 JSON 引号相冲突。|  
|[]|可以使用方括号从具有特定索引的数组中获取值。 例如，如果某个操作将 `range(0,10)` 传入 `forEach` 函数，则可以使用此函数从数组中获取项：  <p>`myArray[item()]`|  
  
## <a name="functions"></a>函数  

还可以在表达式中调用函数。 下表显示了可在表达式中使用的函数。  
  
|表达式|计算|  
|----------------|----------------|  
|"@function('Hello')"|调用第一个参数为文本字符串 Hello 的定义的函数成员。|  
|"@function('It''s Cool!')"|调用第一个参数为文本字符串“It's Cool!”的定义的 函数成员|  
|"@function().prop1"|返回定义的 `myfunction` 成员的 prop1 属性值。|  
|"@function('Hello').prop1"|调用第一个参数为文本字符串“Hello”的定义的函数成员，并返回对象的 prop1 属性。|  
|"@function(parameters('Hello'))"|计算 Hello 参数并将值传递给函数|  
  
### <a name="referencing-functions"></a>引用函数  

可以使用这些函数来引用逻辑应用中其他操作的输出，或者引用创建逻辑应用时传入的值。 例如，可以引用一个步骤中的数据，以便在另一个步骤中使用。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|parameters|返回定义中定义的参数值。 <p>`parameters('password')` <p> **参数数目**：1 <p> **名称**：参数 <p> **说明**：必需。 需要获取其值的参数的名称。|  
|action|使表达式能够从当前运行时操作的其他 JSON 名称值对或输出中派生其值。 以下示例中 propertyPath 表示的属性是可选的。 如果未指定 propertyPath，则引用整个操作对象。 只能在操作的 do-until 条件中使用此函数。 <p>`action().outputs.body.propertyPath`|  
|actions|使表达式能够从运行时操作的其他 JSON 名称值对或输出中派生其值。 这些表达式显式声明某个操作依赖于另一个操作。 以下示例中 propertyPath 表示的属性是可选的。 如果未指定 propertyPath，则引用整个操作对象。 可以使用此元素或 conditions 元素来指定依赖关系，但不需要同时使用它们用于同一依赖资源。 <p>`actions('myAction').outputs.body.propertyPath` <p> **参数数目**：1 <p> **名称**：操作名称 <p> **说明**：必需。 需要获取其值的操作的名称。 <p> 操作对象上的可用属性有： <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>有关这些属性的详细信息，请参阅 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646)。|
|trigger|使表达式能够从运行时触发器的其他 JSON 名称值对或输出中派生其值。 以下示例中 propertyPath 表示的属性是可选的。 如果未指定 propertyPath，则引用整个触发器对象。 <p>`trigger().outputs.body.propertyPath` <p>在触发器的输入中使用时，该函数返回上一次执行的输出。 但是，在触发器的条件中使用时，`trigger` 函数将返回当前执行的输出。 <p> 触发器对象上的可用属性有： <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>有关这些属性的详细信息，请参阅 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644)。|
|actionOutputs|此函数是 `actions('actionName').outputs` 的简写形式 <p> **参数数目**：1 <p> **名称**：操作名称 <p> **说明**：必需。 需要获取其值的操作的名称。|  
|actionBody 和 body|这些函数是 `actions('actionName').outputs.body` 的简写形式 <p> **参数数目**：1 <p> **名称**：操作名称 <p> **说明**：必需。 需要获取其值的操作的名称。|  
|triggerOutputs|此函数是 `trigger().outputs` 的简写形式|  
|triggerBody|此函数是 `trigger().outputs.body` 的简写形式|  
|item|在重复的操作中使用时，此函数返回此次操作迭代的数组中的项。 例如，如果某个操作针对消息数组中的每个项运行，则可以使用以下语法： <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>集合函数  

这些函数针对集合运行，通常适用于数组、字符串，有时适用于字典。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|contains|如果字典包含键、列表包含值，或字符串包含子字符串，则返回 true。 例如，以下函数返回 `true`： <p>`contains('abacaba','aca')` <p> **参数数目**：1 <p> **名称**：集合内 <p> **说明**：必需。 要在其中搜索的集合。 <p> **参数数目**：2 <p> **名称**：查找对象 <p> **说明**：必需。 要在**集合内**查找的对象。|  
|length|返回数组或字符串中的元素数目。 例如，以下函数返回 `3`：  <p>`length('abc')` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要获取其长度的集合。|  
|empty|如果对象、数组或字符串为空，则返回 true。 例如，以下函数返回 `true`： <p>`empty('')` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要检查是否为空的集合。|  
|交集|返回在传入的数组与对象之间具有共同元素的单个数组或对象。 例如，以下函数返回 `[1, 2]`： <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>该函数的参数可以是对象集或数组集（但不能是两者的混合）。 如果存在两个同名的对象，最终的对象中会显示使用该名称的最后一个对象。 <p> **参数数目**：1 ... *n* <p> **名称**：Collection *n* <p> **说明**：必需。 要计算的集合。 要在结果中显示某个对象，该对象必须在传入的所有集合中。|  
|union|返回其所有元素出现在传递给此函数的数组或对象中的单个数组或对象。 例如，以下函数返回 `[1, 2, 3, 10, 101]`： <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>该函数的参数可以是对象集或数组集（但不能是两者的混合）。 如果最终的输出中存在两个同名的对象，最终的对象中会显示使用该名称的最后一个对象。 <p> **参数数目**：1 ... *n* <p> **名称**：Collection *n* <p> **说明**：必需。 要计算的集合。 显示在任意集合中的对象也会显示在结果中。|  
|first|返回传入的数组或字符串中的第一个元素。 例如，以下函数返回 `0`： <p>`first([0,2,3])` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要从中获取第一个对象的集合。|  
|last|返回传入的数组或字符串中的最后一个元素。 例如，以下函数返回 `3`： <p>`last('0123')` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要从中获取最后一个对象的集合。|  
|take|返回传入的数组或字符串中的第一个 **Count** 元素。 例如，以下函数返回 `[1, 2]`：  <p>`take([1, 2, 3, 4], 2)` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要从中获取第一个 **Count** 对象的集合。 <p> **参数数目**：2 <p> **名称**：计数 <p> **说明**：必需。 要从 **Collection** 中获取的对象数目。 必须是正整数。|  
|skip|返回数组中以索引 **Count** 开头的元素。 例如，以下函数返回 `[3, 4]`： <p>`skip([1, 2 ,3 ,4], 2)` <p> **参数数目**：1 <p> **名称**：集合 <p> **说明**：必需。 要从中跳过第一个 **Count** 对象的集合。 <p> **参数数目**：2 <p> **名称**：计数 <p> **说明**：必需。 要在 **Collection** 的前面删除的对象数目。 必须是正整数。|  
|join|返回一个字符串，其中包含通过分隔符联接的数组的每个项。例如，以下函数返回 `"1,2,3,4"`：<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要从中联接项的集合。<br /><br /> **参数数目**：2<br /><br /> **名称**：分隔符<br /><br /> **说明**：必需。 用于分隔项的字符串。|  
  
### <a name="string-functions"></a>字符串函数

以下函数只适用于字符串。 还可以针对字符串使用某些集合函数。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|concat|将任意数量的字符串合并在一起。 例如，如果参数 1 为 `p1`，则以下函数返回 `somevalue-p1-somevalue`： <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **参数数目**：1 ... *n* <p> **名称**：字符串 *n* <p> **说明**：必需。 要合并成单个字符串的字符串。|  
|substring|返回字符串中的字符子集。 例如，以下函数返回 `abc`： <p>`substring('somevalue-abc-somevalue',10,3)` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要从中获取子字符串的字符串。 <p> **参数数目**：2 <p> **名称**：起始索引 <p> **说明**：必需。 参数 1 中子字符串的起始索引。 <p> **参数数目**：3 <p> **名称**：长度 <p> **说明**：必需。 子字符串的长度。|  
|replace|将某个字符串替换为给定的字符串。 例如，以下函数返回 `the new string`： <p>`replace('the old string', 'old', 'new')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 在参数 1 中找到参数 2 时，要为参数 2 搜索的并使用参数 3 更新的字符串。 <p> **参数数目**：2 <p> **名称**：旧字符串 <p> **说明**：必需。 在参数 1 中找到匹配项时，要替换为参数 3 的字符串 <p> **参数数目**：3 <p> **名称**：新字符串 <p> **说明**：必需。 在参数 1 中找到匹配项时，用于替换参数 2 中字符串的字符串。|  
|guid|此函数生成全局唯一字符串 (GUID)。 例如，以下函数生成此 GUID：`c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **参数数目**：1 <p> **名称**：格式 <p> **说明**：可选 单个格式说明符，指示[如何设置此 GUID 值的格式](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)。 格式参数可以是“N”、“D”、“B”、“P”或“X”。 如果未提供格式，则使用“D”。|  
|toLower|将字符串转换为小写。 例如，以下函数返回 `two by two is four`： <p>`toLower('Two by Two is Four')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要转换为小写的字符串。 如果字符串中的某个字符没有小写形式，则在返回的字符串中按原样包含该字符。|  
|toUpper|将字符串转换为大写。 例如，以下函数返回 `TWO BY TWO IS FOUR`： <p>`toUpper('Two by Two is Four')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要转换为大写的字符串。 如果字符串中的某个字符没有大写形式，则在返回的字符串中按原样包含该字符。|  
|indexof|在字符串中不区分大小写查找值的索引。 例如，以下函数返回 `7`： <p>`indexof('hello, world.', 'world')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 可能包含该值的字符串。 <p> **参数数目**：2 <p> **名称**：字符串 <p> **说明**：必需。 要搜索其索引的值。|  
|lastindexof|在字符串中不区分大小写查找值的最后一个索引。 例如，以下函数返回 `3`： <p>`lastindexof('foofoo', 'foo')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 可能包含该值的字符串。 <p> **参数数目**：2 <p> **名称**：字符串 <p> **说明**：必需。 要搜索其索引的值。|  
|startswith|检查字符串是否以某个值开头（不区分大小写）。 例如，以下函数返回 `true`： <p>`startswith('hello, world', 'hello')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 可能包含该值的字符串。 <p> **参数数目**：2 <p> **名称**：字符串 <p> **说明**：必需。 可能为字符串开头的值。|  
|endswith|检查字符串是否以某个值结尾（不区分大小写）。 例如，以下函数返回 `true`： <p>`endswith('hello, world', 'world')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 可能包含该值的字符串。 <p> **参数数目**：2 <p> **名称**：字符串 <p> **说明**：必需。 可能为字符串结尾的值。|  
|split|使用分隔符拆分字符串。 例如，以下函数返回 `["a", "b", "c"]`： <p>`split('a;b;c',';')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要拆分的字符串。 <p> **参数数目**：2 <p> **名称**：字符串 <p> **说明**：必需。 分隔符。|  
  
### <a name="logical-functions"></a>逻辑函数  

这些函数可在条件中使用，并可用于评估任何类型的逻辑。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|equals|如果两个值相等，则返回 true。 例如，如果 parameter1 为 someValue，则以下函数返回 `true`： <p>`equals(parameters('parameter1'), 'someValue')` <p> **参数数目**：1 <p> **名称**：对象 1 <p> **说明**：必需。 要与**对象 2** 比较的对象。 <p> **参数数目**：2 <p> **名称**：对象 2 <p> **说明**：必需。 要与**对象 1** 比较的对象。|  
|less|如果第一个参数小于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下函数返回 `true`： <p>`less(10,100)` <p> **参数数目**：1 <p> **名称**：对象 1 <p> **说明**：必需。 要检查是否小于**对象 2** 的对象。 <p> **参数数目**：2 <p> **名称**：对象 2 <p> **说明**：必需。 要检查是否大于**对象 1** 的对象。|  
|lessOrEquals|如果第一个参数小于或等于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下函数返回 `true`： <p>`lessOrEquals(10,10)` <p> **参数数目**：1 <p> **名称**：对象 1 <p> **说明**：必需。 要检查是否小于或等于**对象 2** 的对象。 <p> **参数数目**：2 <p> **名称**：对象 2 <p> **说明**：必需。 要检查是否大于或等于**对象 1** 的对象。|  
|greater|如果第一个参数大于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下函数返回 `false`：  <p>`greater(10,10)` <p> **参数数目**：1 <p> **名称**：对象 1 <p> **说明**：必需。 要检查是否大于**对象 2** 的对象。 <p> **参数数目**：2 <p> **名称**：对象 2 <p> **说明**：必需。 要检查是否小于**对象 1** 的对象。|  
|greaterOrEquals|如果第一个参数大于或等于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下函数返回 `false`： <p>`greaterOrEquals(10,100)` <p> **参数数目**：1 <p> **名称**：对象 1 <p> **说明**：必需。 要检查是否大于或等于**对象 2** 的对象。 <p> **参数数目**：2 <p> **名称**：对象 2 <p> **说明**：必需。 要检查是否小于或等于**对象 1** 的对象。|  
|and|如果两个参数均为 true，则返回 true。 两个参数需是布尔值。 例如，以下函数返回 `false`： <p>`and(greater(1,10),equals(0,0))` <p> **参数数目**：1 <p> **名称**：布尔值 1 <p> **说明**：必需。 必须为 `true` 的第一个参数。 <p> **参数数目**：2 <p> **名称**：布尔值 2 <p> **说明**：必需。 必须为 `true` 的第二个参数。|  
|或|如果任一参数为 true，则返回 true。 两个参数需是布尔值。 例如，以下函数返回 `true`： <p>`or(greater(1,10),equals(0,0))` <p> **参数数目**：1 <p> **名称**：布尔值 1 <p> **说明**：必需。 可为 `true` 的第一个参数。 <p> **参数数目**：2 <p> **名称**：布尔值 2 <p> **说明**：必需。 可为 `true` 的第二个参数。|  
|not|如果参数为 `false`，则返回 true。 两个参数需是布尔值。 例如，以下函数返回 `true`： <p>`not(contains('200 Success','Fail'))` <p> **参数数目**：1 <p> **名称**：布尔值 <p> **说明**：如果参数为 `false`，则返回 true。 两个参数需是布尔值。 以下函数返回 `true`：`not(contains('200 Success','Fail'))`|  
|if|根据表达式生成的是 `true` 还是 `false` 来返回指定的值。  例如，以下函数返回 `"yes"`： <p>`if(equals(1, 1), 'yes', 'no')` <p> **参数数目**：1 <p> **名称**：表达式 <p> **说明**：必需。 用于确定表达式应返回哪个值的布尔值。 <p> **参数数目**：2 <p> **名称**：True <p> **说明**：必需。 表达式为 `true` 时要返回的值。 <p> **参数数目**：3 <p> **名称**：False <p> **说明**：必需。 表达式为 `false` 时要返回的值。|  
  
### <a name="conversion-functions"></a>转换函数  

这些函数用于在语言中的每个本机类型之间转换：  
  
- 字符串  
  
- integer  
  
- float  
  
- 布尔值  
  
- arrays  
  
- dictionaries  

-   forms  
  
|函数名称|说明|  
|-------------------|-----------------|  
|int|将参数转换为整数。 例如，以下函数返回数字而不是字符串形式的 100： <p>`int('100')` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为整数的值。|  
|字符串|将参数转换为字符串。 例如，以下函数返回 `'10'`： <p>`string(10)` <p>也可以将对象转换为字符串。 例如，如果 `myPar` 参数是包含一个 `abc : xyz` 属性的对象，则以下函数返回 `{"abc" : "xyz"}`： <p>`string(parameters('myPar'))` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为字符串的值。|  
|json|将参数转换为 JSON 类型值，这与 `string()` 相反。 例如，以下函数返回数组而不是字符串形式的 `[1,2,3]`： <p>`json('[1,2,3]')` <p>同样，可以将字符串转换为对象。 例如，以下函数返回 `{ "abc" : "xyz" }`： <p>`json('{"abc" : "xyz"}')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要转换为本机类型值的字符串。 <p>`json()` 函数也支持 XML 输入。 例如，以下对象的参数值： <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>将转换为以下 JSON： <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|将参数自变量转换为浮点数。 例如，以下函数返回 `10.333`： <p>`float('10.333')` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为浮点数的值。|  
|bool|将参数转换为布尔值。 例如，以下函数返回 `false`： <p>`bool(0)` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为布尔值的值。|  
|base64|返回输入字符串的 base64 表示形式。 例如，以下函数返回 `c29tZSBzdHJpbmc=`： <p>`base64('some string')` <p> **参数数目**：1 <p> **名称**：字符串 1 <p> **说明**：必需。 要编码为 base64 表示形式的字符串。|  
|base64ToBinary|返回 base64 编码字符串的二进制表示形式。 例如，以下函数返回 `some string` 二进制表示形式： <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 base64 编码的字符串。|  
|base64ToString|返回 based64 编码字符串的字符串表示形式。 例如，以下函数返回 `some string`： <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 base64 编码的字符串。|  
|二进制|返回某个值的二进制表示形式。  例如，以下函数返回 `some string` 二进制表示形式： <p>`binary('some string')` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为二进制值的值。|  
|dataUriToBinary|返回数据 URI 的二进制表示形式。 例如，以下函数返回 `some string` 二进制表示形式： <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要转换为二进制表示形式的数据 URI。|  
|dataUriToString|返回数据 URI 的字符串表示形式。 例如，以下函数返回 `some string`： <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串<p> **说明**：必需。 要转换为字符串表示形式的数据 URI。|  
|dataUri|返回某个值的数据 URI。 例如，以下函数返回数据 URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`： <p>`dataUri('some string')` <p> **参数数目**：1<p> **名称**：值<p> **说明**：必需。 要转换为数据 URI 的值。|  
|decodeBase64|返回 based64 输入字符串的字符串表示形式。 例如，以下函数返回 `some string`： <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：返回 based64 输入字符串的字符串表示形式。|  
|encodeUriComponent|对传入的字符串进行 URL 转义。 例如，以下函数返回 `You+Are%3ACool%2FAwesome`： <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要对其中的 URL 不安全字符进行转义的字符串。|  
|decodeUriComponent|对传入的字符串取消 URL 转义。 例如，以下函数返回 `You Are:Cool/Awesome`： <p>`decodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要在其中解码 URL 不安全字符的字符串。|  
|decodeDataUri|返回数据 URI 输入字符串的二进制表示形式。 例如，以下函数返回 `some string` 二进制表示形式： <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **参数数目**：1 <p> **名称**：字符串 <p> **说明**：必需。 要解码为二进制表示形式的 dataURI。|  
|uriComponent|返回某个值的 URI 编码表示形式。 例如，以下函数返回 `You+Are%3ACool%2FAwesome`： <p>`uriComponent('You Are:Cool/Awesome')` <p> **参数数目**：1<p> **名称**：字符串 <p> **说明**：必需。 要进行 URI 编码的字符串。|  
|uriComponentToBinary|返回 URI 编码字符串的二进制表示形式。 例如，以下函数返回 `You Are:Cool/Awesome` 二进制表示形式： <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **参数数目**：1 <p> **名称**：字符串<p> **说明**：必需。 URI 编码的字符串。|  
|uriComponentToString|返回 URI 编码字符串的字符串表示形式。 例如，以下函数返回 `You Are:Cool/Awesome`： <p>`uriComponentToString('You+Are%3ACool%2FAwesome')` <p> **参数数目**：1<p> **名称**：字符串<p> **说明**：必需。 URI 编码的字符串。|  
|xml|返回值的 XML 表示形式。 例如，以下函数返回 `'\<name>Alan\</name>'` 表示的 XML 内容： <p>`xml('\<name>Alan\</name>')` <p>`xml()` 函数也支持 JSON 对象输入。 例如，参数 `{ "abc": "xyz" }` 将转换为 XML 内容：`\<abc>xyz\</abc>` <p> **参数数目**：1<p> **名称**：值<p> **说明**：必需。 要转换为 XML 的值。|  
|数组|将参数转换为数组。 例如，以下函数返回 `["abc"]`： <p>`array('abc')` <p> **参数数目**：1 <p> **名称**：值 <p> **说明**：必需。 要转换为数组的值。|
|createArray|从参数创建数组。 例如，以下函数返回 `["a", "c"]`： <p>`createArray('a', 'c')` <p> **参数数目**：1 ... *n* <p> **名称**：Any *n* <p> **说明**：必需。 要合并成数组的值。|
|triggerFormDataValue|返回与表单数据或表单编码触发器输出中的键名匹配的单个值。  如果有多个匹配项，会出错。  例如，以下函数返回 `bar`：`triggerFormDataValue('foo')`<br /><br />**参数数目**：1<br /><br />**名称**：键名<br /><br />**说明**：必需。 要返回的表单数据值的键名。|
|triggerFormDataMultiValues|返回与表单数据或表单编码触发器输出中的键名匹配的值数组。  例如，以下函数返回 `["bar"]`：`triggerFormDataValue('foo')`<br /><br />**参数数目**：1<br /><br />**名称**：键名<br /><br />**说明**：必需。 要返回的表单数据值的键名。|
|triggerMultipartBody|返回触发器的多部件输出中的某个部件的正文。<br /><br />**参数数目**：1<br /><br />**名称**：索引<br /><br />**说明**：必需。 要检索的部件的索引。|
|formDataValue|返回与表单数据或表单编码操作输出中的键名匹配的单个值。  如果有多个匹配项，会出错。  例如，以下函数返回 `bar`：`formDataValue('someAction', 'foo')`<br /><br />**参数数目**：1<br /><br />**名称**：操作名称<br /><br />**说明**：必需。 包含表单数据或表单编码响应的操作的名称。<br /><br />**参数数目**：2<br /><br />**名称**：键名<br /><br />**说明**：必需。 要返回的表单数据值的键名。|
|formDataMultiValues|返回与表单数据或表单编码操作输出中的键名匹配的值数组。  例如，以下函数返回 `["bar"]`：`formDataMultiValues('someAction', 'foo')`<br /><br />**参数数目**：1<br /><br />**名称**：操作名称<br /><br />**说明**：必需。 包含表单数据或表单编码响应的操作的名称。<br /><br />**参数数目**：2<br /><br />**名称**：键名<br /><br />**说明**：必需。 要返回的表单数据值的键名。|
|multipartBody|返回操作的多部件输出中的某个部件的正文。<br /><br />**参数数目**：1<br /><br />**名称**：操作名称<br /><br />**说明**：必需。 包含多部件响应的操作的名称。<br /><br />**参数数目**：2<br /><br />**名称**：索引<br /><br />**说明**：必需。 要检索的部件的索引。|

### <a name="manipulation-functions"></a>操作函数
 
这些函数适用于 XML 和对象。
 
|函数名称|说明|  
|-------------------|-----------------| 
|coalesce|返回传入的参数中的第一个非 null 对象。 **注意**：空字符串不为 null。 例如，如果未定义参数 1 和 2，则以下函数返回 `fallback`：  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **参数数目**：1 ... *n* <p> **名称**：Object*n* <p> **说明**：必需。 要检查是否为 null 的对象。|
|addProperty|返回一个具有其他属性的对象。 如果在运行时已存在此属性，将会引发错误。 例如，以下函数返回对象 `{ "abc" : "xyz", "def": "uvw" }`： <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **参数数目**：1 <p> **名称**：对象 <p> **说明**：必需。 要将新属性添加到的对象。 <p> **参数数目**：2 <p> **名称**：属性名称 <p> **说明**：必需。 新属性的名称。 <p> **参数数目**：3 <p> **名称**：值 <p> **说明**：必需。 要分配给新属性的值。|
|setProperty|返回一个对象，该对象具有其他属性或具有已设置为给定值的现有属性。 例如，以下函数返回对象 `{ "abc" : "uvw" }`： <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **参数数目**：1 <p> **名称**：对象 <p> **说明**：必需。 要在其中设置属性的对象。<p> **参数数目**：2 <p> **名称**：属性名称<p> **说明**：必需。 新属性或现有属性的名称。 <p> **参数数目**：3 <p> **名称**：值 <p> **说明**：必需。 要分配给属性的值。|
|removeProperty|返回一个对象，此对象的某个属性已被删除。 如果要删除的属性不存在，则会返回原始对象。 例如，以下函数返回对象 `{ "abc" : "xyz" }`： <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **参数数目**：1 <p> **名称**：对象 <p> **说明**：必需。 要从中删除属性的对象。<p> **参数数目**：2 <p> **名称**：属性名称 <p> **说明**：必需。 要删除的属性名称。 <p>|
|xpath|返回与 xpath 表达式计算结果值的 xpath 表达式匹配的 XML 节点数组。 <p> **示例 1** <p>假设参数 `p1` 的值是以下 XML 的字符串表示形式： <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>此代码：`xpath(xml(parameters('p1')), '/lab/robot/name')` <p>返回 <p>`[ <name>R1</name>, <name>R2</name> ]` <p>而此代码： <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>返回 <p>`13` <p> <p> **示例 2** <p>假设 XML 内容如下： <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>此代码：`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>而此代码： <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>返回 <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>而此代码：`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>返回 <p>``xyz`` <p> **参数数目**：1 <p> **名称**：Xml <p> **说明**：必需。 要在其中计算 XPath 表达式的 XML。 <p> **参数数目**：2 <p> **名称**：XPath <p> **说明**：必需。 要计算的 XPath 表达式。|

### <a name="math-functions"></a>数学函数  

这些函数可用于以下任一数字类型：**整数**和**浮点数**。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|添加|返回两个数字相加的结果。 例如，以下函数返回 `20.333`： <p>`add(10,10.333)` <p> **参数数目**：1 <p> **名称**：被加数 1 <p> **说明**：必需。 要与**被加数 2** 相加的数字。 <p> **参数数目**：2 <p> **名称**：被加数 2 <p> **说明**：必需。 要与**被加数 1** 相加的数字。|  
|sub|返回两个数字相减的结果。 例如，以下函数返回 `-0.333`： <p>`sub(10,10.333)` <p> **参数数目**：1 <p> **名称**：被减数 <p> **说明**：必需。 要从中减除**减数**的数字。 <p> **参数数目**：2 <p> **名称**：减数 <p> **说明**：必需。 要从**被减数**中减除的数字。|  
|mul|返回两个数字相乘的结果。 例如，以下函数返回 `103.33`： <p>`mul(10,10.333)` <p> **参数数目**：1 <p> **名称**：被乘数 1 <p> **说明**：必需。 要与**被乘数 2** 相乘的数字。 <p> **参数数目**：2 <p> **名称**：被乘数 2 <p> **说明**：必需。 要与**被乘数 1** 相乘的数字。|  
|div|返回两个数字相除的结果。 例如，以下函数返回 `1.0333`： <p>`div(10.333,10)` <p> **参数数目**：1 <p> **名称**：被除数 <p> **说明**：必需。 要与**除数**相除的数字。 <p> **参数数目**：2 <p> **名称**：除数 <p> **说明**：必需。 要与**被除数**相除的数字。|  
|mod|返回两个数字相除的余数（取模）。 例如，以下函数返回 `2`： <p>`mod(10,4)` <p> **参数数目**：1 <p> **名称**：被除数 <p> **说明**：必需。 要与**除数**相除的数字。 <p> **参数数目**：2 <p> **名称**：除数 <p> **说明**：必需。 要与**被除数**相除的数字。 相除后将取余数。|  
|min|可通过两种不同的模式来调用此函数。 <p>如果 `min` 采用数组，该函数将返回 `0`： <p>`min([0,1,2])` <p>或者，此函数可以采用值的逗号分隔列表，此时也返回 `0`： <p>`min(0,1,2)` <p> **注意**：所有值必须为数字，因此，如果参数为数组，该数组必须仅包含数字。 <p> **参数数目**：1 <p> **名称**：集合或值 <p> **说明**：必需。 要在其中查找最小值的值数组，或者集的第一个值。 <p> **参数数目**：2 ... *n* <p> **名称**：Value *n* <p> **说明**：可选 如果第一个参数是一个值，则可以传递其他值，这样就会返回所有传递值的最小值。|  
|max|可通过两种不同的模式来调用此函数。 <p>如果 `max` 采用数组，该函数将返回 `2`： <p>`max([0,1,2])` <p>或者，此函数可以采用值的逗号分隔列表，此时也返回 `2`： <p>`max(0,1,2)` <p> **注意**：所有值必须为数字，因此，如果参数为数组，该数组必须仅包含数字。 <p> **参数数目**：1 <p> **名称**：集合或值 <p> **说明**：必需。 要在其中查找最大值的值数组，或者集的第一个值。 <p> **参数数目**：2 ... *n* <p> **名称**：Value *n* <p> **说明**：可选 如果第一个参数是一个值，则可以传递其他值，这样就会返回所有传递值的最大值。|  
|range|生成从特定数字开始的整数数组。 定义返回数组的长度。 <p>例如，以下函数返回 `[3,4,5,6]`： <p>`range(3,4)` <p> **参数数目**：1 <p> **名称**：起始索引 <p> **说明**：必需。 数组中的第一个整数。 <p> **参数数目**：2 <p> **名称**：计数 <p> **说明**：必需。 此值为数组中的整数数目。|  
|rand|生成指定范围内的随机整数（仅包括第一个边界）。 例如，以下函数返回 `0` 或“1”： <p>`rand(0,2)` <p> **参数数目**：1 <p> **名称**：最小值 <p> **说明**：必需。 可返回的最小整数。 <p> **参数数目**：2 <p> **名称**：最大值 <p> **说明**：必需。 此值为可返回的最大整数后的下一个整数。|  
 
### <a name="date-functions"></a>日期函数  

|函数名称|说明|  
|-------------------|-----------------|  
|utcnow|返回字符串形式的当前时间戳，例如：`2017-03-15T13:27:36Z` <p>`utcnow()` <p> **参数数目**：1 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addseconds|将整数秒数添加到传入的字符串时间戳。 秒数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如：`2015-03-15T13:27:00Z` <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **参数数目**：1 <p> **名称**：时间戳 <p> **说明**：必需。 包含时间的字符串。 <p> **参数数目**：2 <p> **名称**：秒 <p> **说明**：必需。 要添加的秒数。 可为负数（减去相应的秒数）。 <p> **参数数目**：3 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addminutes|将整数分钟数添加到传入的字符串时间戳。 分钟数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如：`2015-03-15T14:00:36Z` <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **参数数目**：1 <p> **名称**：时间戳 <p> **说明**：必需。 包含时间的字符串。 <p> **参数数目**：2 <p> **名称**：分钟 <p> **说明**：必需。 要添加的分钟数。 可为负数（减去相应的分钟数）。 <p> **参数数目**：3 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addhours|将整数小时数添加到传入的字符串时间戳。 小时数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如：`2015-03-16T01:27:36Z` <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **参数数目**：1 <p> **名称**：时间戳 <p> **说明**：必需。 包含时间的字符串。 <p> **参数数目**：2 <p> **名称**：小时 <p> **说明**：必需。 要添加的小时数。 可为负数（减去相应的小时数）。 <p> **参数数目**：3 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|adddays|将整数天数添加到传入的字符串时间戳。 天数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如：`2015-03-13T13:27:36Z` <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **参数数目**：1 <p> **名称**：时间戳 <p> **说明**：必需。 包含时间的字符串。 <p> **参数数目**：2 <p> **名称**：天 <p> **说明**：必需。 要添加的天数。 可为负数（减去相应的天数）。 <p> **参数数目**：3 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|formatDateTime|返回日期格式的字符串。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如：`2015-03-15T13:27:36Z` <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **参数数目**：1 <p> **名称**：日期 <p> **说明**：必需。 包含日期的字符串。 <p> **参数数目**：2 <p> **名称**：格式 <p> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|startOfHour|返回传入的字符串时间戳的起始小时。 例如 `2017-03-15T13:00:00Z`：<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。<br /><br />**参数数目**：2<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|startOfDay|返回传入的字符串时间戳的起始日期。 例如 `2017-03-15T00:00:00Z`：<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。<br /><br />**参数数目**：2<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。| 
|startOfMonth|返回传入的字符串时间戳的起始月份。 例如 `2017-03-01T00:00:00Z`：<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。<br /><br />**参数数目**：2<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。| 
|dayOfWeek|返回字符串时间戳的星期日期组成部分。  星期日为 0，星期一为 1，依此类推。 例如 `3`：<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。| 
|dayOfMonth|返回字符串时间戳的月份日期组成部分。 例如 `15`：<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。| 
|dayOfYear|返回字符串时间戳的年份日期组成部分。 例如 `74`：<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。| 
|ticks|返回字符串时间戳的计时周期属性。 例如 `1489603019`：<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 这是包含时间的字符串。| 
  
### <a name="workflow-functions"></a>工作流函数  

这些函数可在运行时帮助获取有关工作流本身的信息。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|listCallbackUrl|返回用于调用触发器或操作的字符串。 <p> **注意**：此函数只能在 **httpWebhook** 和 **apiConnectionWebhook** 中使用，而不能在 **manual**、**recurrence**、**http** 或 **apiConnection** 中使用。 <p>例如，`listCallbackUrl()` 函数返回： <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|工作流|此函数提供工作流本身在运行时的所有详细信息。 <p> 工作流对象上的可用属性有： <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> `run` 属性的值是具有以下属性的对象： <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>有关这些属性的详细信息，请参阅 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617)。<p> 例如，若要获取当前运行的名称，请使用 `"@workflow().run.name"` 表达式。 |

## <a name="next-steps"></a>后续步骤

[工作流操作和触发器](logic-apps-workflow-actions-triggers.md)
