---
title: 条件认知搜索技能（Azure 搜索）| Microsoft Docs
description: 使用条件技能可以筛选、创建默认值以及合并值。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 29e50a3d978338eaa46566574e6a20685a14bda4
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186396"
---
#   <a name="conditional-skill"></a>条件技能

条件技能可以实现需要使用布尔运算来确定要分配到输出的数据的 Azure 搜索方案。 这些方案包括基于条件进行筛选、分配默认值以及合并数据。

以下伪代码演示条件技能可以实现的功能：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 此技能未绑定到 Azure 认知服务 API，使用它无需付费。 但是，仍需[附加认知服务资源](cognitive-search-attach-cognitive-services.md)，以替代局限于每日只能进行少量扩充的“免费”资源选项。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>求值的字段

此技能非常特别，因为它的输入是求值的字段。

以下各项是表达式的有效值：

-   批注路径（表达式中的路径必须以“$(”和“)”分隔）
 <br/>
    例如：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  文本（字符串、数字、true、false、null） <br/>
    例如：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比较运算符（==、!=、>=、>、<=、<）的表达式 <br/>
    例如：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布尔运算符（&&、||、!、^）的表达式 <br/>
    例如：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用数字运算符（+、-、\*、/、%）的表达式 <br/>
    例如： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由于条件技能支持求值，因此可以在次要转换方案中使用它。 有关示例，请参阅[技能定义 4](#transformation-example)。

## <a name="skill-inputs"></a>技能输入
输入区分大小写。

| 输入   | 描述 |
|-------------|-------------|
| condition   | 此输入是一个[求值字段](#evaluated-fields)，表示要求值的条件。 此条件应求值为布尔值（*true* 或 *false*）。   <br/>  例如： <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 此输入是一个[求值字段](#evaluated-fields)，表示当条件求值为 *true* 时要返回的值。 常量字符串应括在单引号（' 和 '）中返回。 <br/>示例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 此输入是一个[求值字段](#evaluated-fields)，表示当条件求值为 *false* 时要返回的值。 <br/>示例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>技能输出
有一个简称为“输出”的输出。 如果条件为 false，它会返回值 *whenFalse*；如果条件为 true，它会返回值 *whenTrue*。

## <a name="examples"></a>示例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>示例技能定义 1：筛选文档，以仅返回法语文档

如果文档的语言为法语，则以下输出将返回句子数组 ("/document/frenchSentences")。 如果语言不是法语，值将设置为 *null*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
如果将“/document/frenchSentences”用作另一技能的上下文，仅当“/document/frenchSentences”未设置为 *null* 时，才运行该技能。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>示例技能定义 2：为不存在的值设置默认值

以下输出创建批注 ("/document/languageWithDefault")，该批注设置为文档的语言，如果未设置该语言，则该批注将设置为“es”。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>示例技能定义 3：将两个字段中的值合并为一个

在此示例中，某些句子包含 *frenchSentiment* 属性。 每当 *frenchSentiment* 属性为 null 时，我们就要使用 *englishSentiment* 值。 将输出分配到名为 *sentiment* 的成员 ("/document/sentiment/*/sentiment")。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>转换示例
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>示例技能定义 4：基于单个字段进行数据转换

在此示例中，我们将收到介于 0 和 1 之间的情绪。 我们希望将此值转换为 -1 到 1。 可以使用条件技能来实现这种次要转换。

在此示例中，我们未使用技能的条件性，因为该条件始终为 *true*。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>特殊注意事项
某些参数将会求值，因此需要特别谨慎地遵循阐述的模式。 表达式必须以等号开头。 路径必须以“$(”和“)”分隔。 请务必将字符串放在单引号中。 这有助于求值程序区分字符串与实际的路径和运算符。 此外，请务必在运算符的两侧添加空格（例如，在路径中使用 "*" 表示不是乘号）。


## <a name="next-steps"></a>后续步骤

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
