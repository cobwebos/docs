---
title: "Azure 数据工厂中的表达式和函数 | Microsoft Docs"
description: "本文提供了关于在创建数据工厂实体时可以使用的表达式和函数的信息。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: eee276f2bcf6a8b7b2c79139bfeb01e1ebf761c9
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure 数据工厂中的表达式和函数
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-functions-variables.md)
> * [版本 2 - 预览版](control-flow-expression-language-functions.md)

本文提供了有关 Azure 数据工厂（版本 2）支持的表达式和函数的详细信息。 

## <a name="introduction"></a>介绍
定义中的 JSON 值可以是文字，也可以是运行时计算的表达式。 例如：  
  
```json
"name": "value"
```

 （或者）  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)） ，请参阅[数据工厂 V1 中的函数和变量](v1/data-factory-functions-variables.md)。


## <a name="expressions"></a>表达式  
表达式可出现在 JSON 字符串值中的任何位置，始终生成另一个 JSON 值。 如果某个 JSON 值为表达式，会通过删除 @ 符号来提取表达式的正文。 如果需要以 @ 开头的文本字符串，则必须使用 @@ 将它转义。 以下示例演示了如何计算表达式。  
  
|JSON 值|结果|  
|----------------|------------|  
|"parameters"|返回字符“parameters”。|  
|"parameters[1]"|返回字符“parameters[1]”。|  
|"@@"|返回包含“@”的、由 1 个字符构成的字符串。|  
|" @"|返回包含“@”的、由 2 个字符构成的字符串。|  
  
 如果使用称为字符串内插的功能（其中表达式封装在 `@{ ... }` 内），表达式还可以显示在字符串内。 例如： `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 使用字符串内插，结果始终是字符串。 假设我将 `myNumber` 定义为 `42`，将 `myString` 定义为 `foo`：  
  
|JSON 值|结果|  
|----------------|------------|  
|"@pipeline().parameters.myString"| 返回字符串形式的 `foo`。|  
|"@{pipeline().parameters.myString}"| 返回字符串形式的 `foo`。|  
|"@pipeline().parameters.myNumber"| 返回*数字*形式的 `42`。|  
|"@{pipeline().parameters.myNumber}"| 返回*字符串*形式的 `42`。|  
|"Answer is: @{pipeline().parameters.myNumber}"| 返回字符串 `Answer is: 42`。|  
|"@concat('Answer is: ', string(pipeline().parameters.myNumber))"| 返回字符串 `Answer is: 42`|  
|"Answer is: @@{pipeline().parameters.myNumber}"| 返回字符串 `Answer is: @{pipeline().parameters.myNumber}`。|  
  
### <a name="examples"></a>示例

#### <a name="a-dataset-with-a-parameter"></a>使用参数的数据集
在以下示例中，BlobDataset 采用名为 **path** 的参数。 其值用于使用以下表达式设置 **folderPath** 属性的值：`@{dataset().path}`。 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>使用参数的管道
在以下示例中，管道采用 **inputPath** 和 **outputPath** 参数。 参数化 blob 数据集的**路径**使用这些参数的值进行设置。 此处使用的语法是：`pipeline().parameters.parametername`。 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>函数  
 可以在表达式中调用函数。 以下各节提供了有关可以在表达式中使用的函数的信息。  

## <a name="string-functions"></a>字符串函数  
 以下函数只适用于字符串。 还可以针对字符串使用一些集合函数。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|concat|将任意数量的字符串合并在一起。 例如，如果 parameter1 为 `foo,`，以下表达式将返回 `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **参数数目**：1 ... *n*<br /><br /> **名称**：字符串 *n*<br /><br /> **说明**：必需。 要合并成单个字符串的字符串。|  
|substring|返回字符串中的字符子集。 例如，以下表达式：<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> 返回：<br /><br /> `foo`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要从中获取子字符串的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：起始索引<br /><br /> **说明**：必需。 参数 1 中子字符串的起始索引。<br /><br /> **参数数目**：3<br /><br /> **名称**：长度<br /><br /> **说明**：必需。 子字符串的长度。|  
|replace|将某个字符串替换为给定的字符串。 例如，表达式：<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> 返回：<br /><br /> `the new string`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。  如果在参数 1 中找到参数 2，即要为参数 2 搜索并使用参数 3 更新的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：旧字符串<br /><br /> **说明**：必需。 在参数 1 中找到匹配项时，要替换为参数 3 的字符串<br /><br /> **参数数目**：3<br /><br /> **名称**：新字符串<br /><br /> **说明**：必需。 在参数 1 中找到匹配项时，用于替换参数 2 中字符串的字符串。|  
|guid| 生成全局唯一字符串（又称 guid）。 例如，可以生成下面的输出 `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`：<br /><br /> `guid()`<br /><br /> **参数数目**：1<br /><br /> **名称**：格式<br /><br /> **说明**：可选 单个格式说明符，指示[如何设置此 GUID 值的格式](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)。 格式参数可以是“N”、“D”、“B”、“P”或“X”。 如果未提供格式，则使用“D”。|  
|toLower|将字符串转换为小写。 例如，以下表达式返回 `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要转换为小写的字符串。 如果字符串中的某个字符没有对应的小写形式，则在返回的字符串中按原样包含该字符。|  
|toUpper|将字符串转换为大写。 例如，以下表达式返回 `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要转换为大写的字符串。 如果字符串中的某个字符没有对应的大写形式，则在返回的字符串中按原样包含该字符。|  
|indexof|在字符串中不区分大小写查找值的索引。 例如，以下表达式返回 `7`: `indexof('hello, world.', 'world')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能包含该值的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要搜索其索引的值。|  
|lastindexof|在字符串中不区分大小写查找值的最后一个索引。 例如，以下表达式返回 `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能包含该值的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要搜索其索引的值。|  
|startswith|检查字符串是否以某个值开头（不区分大小写）。 例如，以下表达式返回 `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能包含该值的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能为字符串开头的值。|  
|endswith|检查字符串是否以某个值结尾（不区分大小写）。 例如，以下表达式返回 `true`: `lastindexof('hello, world', 'world')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能包含该值的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 可能为字符串结尾的值。|  
|split|使用分隔符拆分字符串。 例如，以下表达式返回 `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要拆分的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 分隔符。|  
  
  
## <a name="collection-functions"></a>集合函数  
 这些函数针对集合运行，例如数组、字符串，有时是字典。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|contains|如果字典包含键、列表包含值，或字符串包含子字符串，则返回 true。 例如，以下表达式返回 `true:``contains('abacaba','aca')`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合内<br /><br /> **说明**：必需。 要在其中搜索的集合。<br /><br /> **参数数目**：2<br /><br /> **名称**：查找对象<br /><br /> **说明**：必需。 要在**集合内**查找的对象。|  
|length|返回数组或字符串中的元素数目。 例如，以下表达式返回 `3`:  `length('abc')`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要获取长度的集合。|  
|empty|如果对象、数组或字符串为空，则返回 true。 例如，以下表达式返回 `true`:<br /><br /> `empty('')`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要检查是否为空的集合。|  
|交集|返回在传递给此函数的数组与对象之间具有共同元素的单个数组或对象。 例如，以下函数返回 `[1, 2]`：<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> 该函数的参数可以是对象集或数组集（但不能是两者的混合）。 如果存在两个同名的对象，最终的对象中会显示使用该名称的最后一个对象。<br /><br /> **参数数目**：1 ... *n*<br /><br /> **名称**：集合 *n*<br /><br /> **说明**：必需。 要计算的集合。 要在结果中显示某个对象，该对象必须在传入的所有集合中。|  
|union|返回其所有元素出现在传递给此函数的数组或对象中的单个数组或对象。 例如，此函数返回 `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> 该函数的参数可以是对象集或数组集（但不能是两者的混合）。 如果最终的输出中存在两个同名的对象，最终的对象中会显示使用该名称的最后一个对象。<br /><br /> **参数数目**：1 ... *n*<br /><br /> **名称**：集合 *n*<br /><br /> **说明**：必需。 要计算的集合。 显示在任意集合中的对象会显示在结果中。|  
|first|返回传入的数组或字符串中的第一个元素。 例如，以下函数返回 `0`：<br /><br /> `first([0,2,3])`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要从中获取第一个对象的集合。|  
|last|返回传入的数组或字符串中的最后一个元素。 例如，以下函数返回 `3`：<br /><br /> `last('0123')`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要从中获取最后一个对象的集合。|  
|take|返回传入的数组或字符串中的第一个 Count 元素，例如，此函数返回 `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要从中获取第一个 Count 对象的集合。<br /><br /> **参数数目**：2<br /><br /> **名称**：计数<br /><br /> **说明**：必需。 要从 **Collection** 中获取的对象数目。 必须是正整数。|  
|skip|返回数组中以索引 Count 开头的元素，例如，此函数返回 `[3, 4]`：<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **参数数目**：1<br /><br /> **名称**：集合<br /><br /> **说明**：必需。 要从中跳过第一个 **Count** 对象的集合。<br /><br /> **参数数目**：2<br /><br /> **名称**：计数<br /><br /> **说明**：必需。 要在 **Collection** 的前面删除的对象数目。 必须是正整数。|  
  
## <a name="logical-functions"></a>逻辑函数  
 这些函数可在条件中使用，并可用于评估任何类型的逻辑。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|equals|如果两个值相等，则返回 true。 例如，如果 parameter1 为 foo，以下表达式返回 `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **参数数目**：1<br /><br /> **名称**：对象 1<br /><br /> **说明**：必需。 要与**对象 2** 比较的对象。<br /><br /> **参数数目**：2<br /><br /> **名称**：对象 2<br /><br /> **说明**：必需。 要与**对象 1** 比较的对象。|  
|less|如果第一个参数小于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下表达式返回 `true`:  `less(10,100)`<br /><br /> **参数数目**：1<br /><br /> **名称**：对象 1<br /><br /> **说明**：必需。 要检查是否小于**对象 2** 的对象。<br /><br /> **参数数目**：2<br /><br /> **名称**：对象 2<br /><br /> **说明**：必需。 要检查是否大于**对象 1** 的对象。|  
|lessOrEquals|如果第一个参数小于或等于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下表达式返回 `true`:  `lessOrEquals(10,10)`<br /><br /> **参数数目**：1<br /><br /> **名称**：对象 1<br /><br /> **说明**：必需。 要检查是否小于或等于**对象 2** 的对象。<br /><br /> **参数数目**：2<br /><br /> **名称**：对象 2<br /><br /> **说明**：必需。 要检查是否大于或等于**对象 1** 的对象。|  
|greater|如果第一个参数大于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下表达式返回 `false`:  `greater(10,10)`<br /><br /> **参数数目**：1<br /><br /> **名称**：对象 1<br /><br /> **说明**：必需。 要检查是否大于**对象 2** 的对象。<br /><br /> **参数数目**：2<br /><br /> **名称**：对象 2<br /><br /> **说明**：必需。 要检查是否小于**对象 1** 的对象。|  
|greaterOrEquals|如果第一个参数大于或等于第二个参数，则返回 true。 请注意，值的类型只能是整数、浮点数或字符串。 例如，以下表达式返回 `false`:  `greaterOrEquals(10,100)`<br /><br /> **参数数目**：1<br /><br /> **名称**：对象 1<br /><br /> **说明**：必需。 要检查是否大于或等于**对象 2** 的对象。<br /><br /> **参数数目**：2<br /><br /> **名称**：对象 2<br /><br /> **说明**：必需。 要检查是否小于或等于**对象 1** 的对象。|  
|and|如果两个参数均为 true，则返回 true。 两个参数需是布尔值。 以下表达式返回 `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **参数数目**：1<br /><br /> **名称**：布尔值 1<br /><br /> **说明**：必需。 必须为 `true` 的第一个参数。<br /><br /> **参数数目**：2<br /><br /> **名称**：布尔值 2<br /><br /> **说明**：必需。 必须为 `true` 的第二个参数。|  
|或|如果两个参数均为 true，则返回 true。 两个参数需是布尔值。 以下表达式返回 `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **参数数目**：1<br /><br /> **名称**：布尔值 1<br /><br /> **说明**：必需。 可为 `true` 的第一个参数。<br /><br /> **参数数目**：2<br /><br /> **名称**：布尔值 2<br /><br /> **说明**：必需。 可为 `true` 的第二个参数。|  
|not|如果参数为 `false`，则返回 true。 两个参数需是布尔值。 以下表达式返回 `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **参数数目**：1<br /><br /> **名称**：布尔值<br /><br /> 说明：如果参数为 `false`，则返回 true。 两个参数需是布尔值。 以下表达式返回 `true`:  `not(contains('200 Success','Fail'))`|  
|if|根据表达式提供的结果是 `true` 还是 `false` 来返回指定的值。  例如，以下表达式返回 `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **参数数目**：1<br /><br /> **名称**：表达式<br /><br /> **说明**：必需。 用于确定表达式返回哪个值的布尔值。<br /><br /> **参数数目**：2<br /><br /> **名称**：True<br /><br /> **说明**：必需。 表达式为 `true` 时要返回的值。<br /><br /> **参数数目**：3<br /><br /> **名称**：False<br /><br /> **说明**：必需。 表达式为 `false` 时要返回的值。|  
  
## <a name="conversion-functions"></a>转换函数  
 这些函数用于在语言中的每个本机类型之间转换：  
  
-   字符串  
  
-   integer  
  
-   float  
  
-   布尔值  
  
-   arrays  
  
-   dictionaries  
  
|函数名称|说明|  
|-------------------|-----------------|  
|int|将参数转换为整数。 例如，以下表达式返回数字而不是字符串形式的 100：`int('100')`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为整数的值。|  
|字符串|将参数转换为字符串。 例如，以下表达式返回 `'10'`:  `string(10)`。你还可以将对象转换为字符串，例如，如果 foo 参数是一个具有属性 `bar : baz` 的对象，那么以下表达式将返回 `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为字符串的值。|  
|json|将参数转换为 JSON 类型值。 它与 string() 相反。 例如，以下表达式返回数组而不是字符串形式的 `[1,2,3]`：<br /><br /> `parse('[1,2,3]')`<br /><br /> 同样，可以将字符串转换为对象。 例如，`json('{"bar" : "baz"}')` 返回：<br /><br /> `{ "bar" : "baz" }`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要转换为本机类型值的字符串。<br /><br /> json 函数也支持 xml 输入。 例如，以下对象的参数值：<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> 被转换为以下 json：<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|将参数自变量转换为浮点数。 例如，以下表达式返回 `10.333`:  `float('10.333')`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为浮点数的值。|  
|bool|将参数转换为布尔值。 例如，以下表达式返回 `false`:  `bool(0)`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为布尔值的值。|  
|coalesce|返回传入的参数中的第一个非 null 对象。 注意：空字符串不为 null。 例如，如果未定义参数 1 和 2，则此函数返回 `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **参数数目**：1 ... *n*<br /><br /> **名称**：对象*n*<br /><br /> **说明**：必需。 要检查是否为 `null` 的对象。|  
|base64|返回输入字符串的 base64 表示形式。 例如，以下表达式返回 `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串 1<br /><br /> **说明**：必需。 要编码为 base64 表示形式的字符串。|  
|base64ToBinary|返回 base64 编码字符串的二进制表示形式。 例如，以下表达式返回某个字符串的二进制表示形式：`base64ToBinary('c29tZSBzdHJpbmc=')`。<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 base64 编码的字符串。|  
|base64ToString|返回 based64 编码字符串的字符串表示形式。 例如，以下表达式返回某个字符串：`base64ToString('c29tZSBzdHJpbmc=')`。<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 base64 编码的字符串。|  
|二进制|返回某个值的二进制表示形式。  例如，以下表达式返回某个字符串的二进制表示形式：`binary(‘some string’).`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为二进制值的值。|  
|dataUriToBinary|返回数据 URI 的二进制表示形式。 例如，以下表达式返回某个字符串的二进制表示形式：`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要转换为二进制表示形式的数据 URI。|  
|dataUriToString|返回数据 URI 的字符串表示形式。 例如，以下表达式返回某个字符串：`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br />**说明**：必需。 要转换为字符串表示形式的数据 URI。|  
|dataUri|返回某个值的数据 URI。 例如，以下表达式返回数据：`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **参数数目**：1<br /><br />**名称**：值<br /><br />**说明**：必需。 要转换为数据 URI 的值。|  
|decodeBase64|返回 based64 输入字符串的字符串表示形式。 例如，以下表达式返回 `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：返回 based64 输入字符串的字符串表示形式。|  
|encodeUriComponent|对传入的字符串进行 URL 转义。 例如，以下表达式返回 `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要对其中的 URL 不安全字符进行转义的字符串。|  
|decodeUriComponent|对传入的字符串取消 URL 转义。 例如，以下表达式返回 `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要在其中解码 URL 不安全字符的字符串。|  
|decodeDataUri|返回数据 URI 输入字符串的二进制表示形式。 例如，以下表达式返回`some string`的二进制表示形式：`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br /> **说明**：必需。 要解码为二进制表示形式的 dataURI。|  
|uriComponent|返回某个值的 URI 编码表示形式。 例如，以下表达式返回 `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> 参数详细信息：数目：1、名称：字符串、说明：必需。 要进行 URI 编码的字符串。|  
|uriComponentToBinary|返回 URI 编码字符串的二进制表示形式。 例如，以下表达式返回`You Are:Cool/Awesome`的二进制表示形式：`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **参数数目**：1<br /><br /> **名称**：字符串<br /><br />**说明**：必需。 URI 编码的字符串。|  
|uriComponentToString|返回 URI 编码字符串的字符串表示形式。 例如，以下表达式返回 `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **参数数目**：1<br /><br />**名称**：字符串<br /><br />**说明**：必需。 URI 编码的字符串。|  
|xml|返回值的 xml 表示形式。 例如，以下表达式返回 xml 内容，表示为：`'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`。 xml 函数也支持 JSON 对象输入。 例如，参数 `{ "abc": "xyz" }` 将转换为 xml 内容 `\<abc>xyz\</abc>`<br /><br /> **参数数目**：1<br /><br />**名称**：值<br /><br />**说明**：必需。 要转换为 XML 的值。|  
|xpath|返回与 xpath 表达式计算结果值的 xpath 表达式匹配的 xml 节点数组。<br /><br />  **示例 1**<br /><br /> 假设参数“p1”的值是以下 XML 的字符串表示形式：<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1.此代码：`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> 将返回<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> whereas<br /><br /> 2.此代码：`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> 将返回<br /><br /> `13`<br /><br /> <br /><br /> **示例 2**<br /><br /> 假设 XML 内容如下：<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.此代码：`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> 或<br /><br /> 2.此代码：`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> 返回<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 3.此代码：`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> 返回<br /><br /> ``bar``<br /><br /> **参数数目**：1<br /><br />**名称**：Xml<br /><br />**说明**：必需。 要在其中计算 XPath 表达式的 XML。<br /><br /> **参数数目**：2<br /><br />**名称**：XPath<br /><br />**说明**：必需。 要计算的 XPath 表达式。|  
|数组|将参数转换为数组。  例如，以下表达式返回 `["abc"]`: `array('abc')`<br /><br /> **参数数目**：1<br /><br /> **名称**：值<br /><br /> **说明**：必需。 要转换为数组的值。|
|createArray|从参数创建数组。  例如，以下表达式返回 `["a", "c"]`: `createArray('a', 'c')`<br /><br /> 参数数目：1 ... n<br /><br /> 名称：Any n<br /><br /> **说明**：必需。 要合并成数组的值。|

## <a name="math-functions"></a>数学函数  
 这些函数可用于以下任一数字类型：**整数**和**浮点数**。  
  
|函数名称|说明|  
|-------------------|-----------------|  
|添加|返回两个数字相加的结果。 例如，此函数返回 `20.333`:  `add(10,10.333)`<br /><br /> **参数数目**：1<br /><br /> **名称**：被加数 1<br /><br /> **说明**：必需。 要与**被加数 2** 相加的数字。<br /><br /> **参数数目**：2<br /><br /> **名称**：被加数 2<br /><br /> **说明**：必需。 要与**被加数 1** 相加的数字。|  
|sub|返回两个数字相减的结果。 例如，此函数返回：`-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **参数数目**：1<br /><br /> **名称**：被减数<br /><br /> **说明**：必需。 要从中减除**减数**的数字。<br /><br /> **参数数目**：2<br /><br /> **名称**：减数<br /><br /> **说明**：必需。 要从**被减数**中减除的数字。|  
|mul|返回两个数字相乘的结果。 例如，以下表达式返回 `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **参数数目**：1<br /><br /> **名称**：被乘数 1<br /><br /> **说明**：必需。 要与**被乘数 2** 相乘的数字。<br /><br /> **参数数目**：2<br /><br /> **名称**：被乘数 2<br /><br /> **说明**：必需。 要与**被乘数 1** 相乘的数字。|  
|div|返回两个数字相除的结果。 例如，以下表达式返回 `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **参数数目**：1<br /><br /> **名称**：被除数<br /><br /> **说明**：必需。 要与**除数**相除的数字。<br /><br /> **参数数目**：2<br /><br /> **名称**：除数<br /><br /> **说明**：必需。 要与**被除数**相除的数字。|  
|mod|返回两个数字相除后的余数结果（取模）。 例如，以下表达式返回 `2`:<br /><br /> `mod(10,4)`<br /><br /> **参数数目**：1<br /><br /> **名称**：被除数<br /><br /> **说明**：必需。 要与**除数**相除的数字。<br /><br /> **参数数目**：2<br /><br /> **名称**：除数<br /><br /> **说明**：必需。 要与**被除数**相除的数字。 相除后将取余数。|  
|min|可通过两种不同的模式来调用此函数：`min([0,1,2])` min 在此取一个数组。 此表达式返回 `0`。 或者，此函数可以采用值的逗号分隔列表：`min(0,1,2)` 此函数也会返回 0。 注意：所有值都必须为数字，因此，如果参数为数组，该数组必须仅包含数字。<br /><br /> **参数数目**：1<br /><br /> **名称**：集合或值<br /><br /> **说明**：必需。 要查找最小值，它可以是一组值，也可以是一组值的第一个值。<br /><br /> **参数数目**：2 ... *n*<br /><br /> **名称**：值 *n*<br /><br /> **说明**：可选 如果第一个参数是一个值，则可以传递其他值，并返回所有传递的值的最小值。|  
|max|可通过两种不同的模式来调用此函数：`max([0,1,2])`<br /><br /> max 在此处采用一个数组。 此表达式返回 `2`。 或者，此函数可以采用值的逗号分隔列表：`max(0,1,2)` 此函数会返回 2。 注意：所有值都必须为数字，因此，如果参数为数组，该数组必须仅包含数字。<br /><br /> **参数数目**：1<br /><br /> **名称**：集合或值<br /><br /> **说明**：必需。 要查找最大值，它可以是一组值，也可以是一组值的第一个值。<br /><br /> **参数数目**：2 ... *n*<br /><br /> **名称**：值 *n*<br /><br /> **说明**：可选 如果第一个参数是一个值，则可以传递其他值，这样就会返回所有传递值的最大值。|  
|range| 生成一个从某个数字开始的整数数组，然后定义返回数组的长度。 例如，以下函数返回 `[3,4,5,6]`：<br /><br /> `range(3,4)`<br /><br /> **参数数目**：1<br /><br /> **名称**：起始索引<br /><br /> **说明**：必需。 它是数组中的第一个整数。<br /><br /> **参数数目**：2<br /><br /> **名称**：计数<br /><br /> **说明**：必需。 数组中的整数个数。|  
|rand| 生成指定范围内的随机整数（包括两个边界。 例如，此函数可能返回 `42`：<br /><br /> `rand(-1000,1000)`<br /><br /> **参数数目**：1<br /><br /> **名称**：最小值<br /><br /> **说明**：必需。 可返回的最小整数。<br /><br /> **参数数目**：2<br /><br /> **名称**：最大值<br /><br /> **说明**：必需。 可返回的最大整数。|  
  
## <a name="date-functions"></a>日期函数  
  
|函数名称|说明|  
|-------------------|-----------------|  
|utcnow|返回字符串形式的当前时间戳。 例如 `2015-03-15T13:27:36Z`：<br /><br /> `utcnow()`<br /><br /> **参数数目**：1<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addseconds|将整数秒数添加到传入的字符串时间戳。 秒数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如 `2015-03-15T13:27:00Z`：<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 包含时间的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：秒<br /><br /> **说明**：必需。 要添加的秒数。 可为负数（减去相应的秒数）。<br /><br /> **参数数目**：3<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addminutes|将整数分钟数添加到传入的字符串时间戳。 分钟数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如，`2015-03-15T14:00:36Z`：<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 包含时间的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：分钟<br /><br /> **说明**：必需。 要添加的分钟数。 可为负数（减去相应的分钟数）。<br /><br /> **参数数目**：3<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|addhours|将整数小时数添加到传入的字符串时间戳。 小时数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如 `2015-03-16T01:27:36Z`：<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 包含时间的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：小时<br /><br /> **说明**：必需。 要添加的小时数。 可为负数（减去相应的小时数）。<br /><br /> **参数数目**：3<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|adddays|将整数天数添加到传入的字符串时间戳。 天数可为正数或负数。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如 `2015-02-23T13:27:36Z`：<br /><br /> `addseconds('2015-03-15T13:27:36Z', -20)`<br /><br /> **参数数目**：1<br /><br /> **名称**：时间戳<br /><br /> **说明**：必需。 包含时间的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：天<br /><br /> **说明**：必需。 要添加的天数。 可为负数（减去相应的天数）。<br /><br /> **参数数目**：3<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  
|formatDateTime|返回日期格式的字符串。 默认情况下，结果是采用 ISO 8601 格式 ("o") 的字符串，除非提供了格式说明符。 例如 `2015-02-23T13:27:36Z`：<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **参数数目**：1<br /><br /> **名称**：日期<br /><br /> **说明**：必需。 包含日期的字符串。<br /><br /> **参数数目**：2<br /><br /> **名称**：格式<br /><br /> **说明**：可选 [单个格式说明符](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自定义格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指示如何设置此时间戳值的格式。 如果未提供格式，则使用 ISO 8601 格式 ("o")。|  

## <a name="next-steps"></a>后续步骤
对于可以在表达式中使用的系统变量列表，请参阅[系统变量](control-flow-system-variables.md)。