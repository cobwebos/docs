---
title: 条件认知技能
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中的条件性技能启用筛选、创建默认值以及合并技能组合定义中的值。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792052"
---
# <a name="conditional-cognitive-skill"></a>条件认知技能

**条件**技能使 Azure 认知搜索方案需要一个布尔运算来确定要分配给输出的数据。 这些方案包括筛选、分配默认值以及根据条件合并数据。

下面的伪代码演示了条件技能的作用：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 此技能未绑定到 Azure 认知服务 API，因此你不需要付费。 不过，您仍应[附加认知服务资源](cognitive-search-attach-cognitive-services.md)来替代 "免费" 资源选项，该选项限制您每天的少量根据。

## <a name="odatatype"></a>@odata.type  
Util. ConditionalSkill


## <a name="evaluated-fields"></a>计算字段

这种技能是特别的，因为其输入是计算字段。

以下各项是表达式的有效值：

-   批注路径（表达式中的路径必须由 "$ （" 和 "）" 分隔）
 <br/>
    示例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  文本（字符串、数字、true、false 和 null） <br/>
    示例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比较运算符（= =、！ =、> =、>、< = <）的表达式 <br/>
    示例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布尔运算符（& &，| |，！，^）的表达式 <br/>
    示例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用数值运算符（+、-、\*、/、%）的表达式 <br/>
    示例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由于条件技能支持评估，因此你可以将其用于小转换方案。 例如，请参阅[技能定义 4](#transformation-example)。

## <a name="skill-inputs"></a>技能输入
输入区分大小写。

| 输入   | 描述 |
|-------------|-------------|
| 条件   | 此输入是一个[计算字段](#evaluated-fields)，表示要计算的条件。 此条件的计算结果应为布尔值（*true*或*false*）。   <br/>  示例： <br/> "= true" <br/> "= $ （/document/language） = = ' fr '" <br/> "= $ （/document/pages/\*/language） = = $ （/document/expectedLanguage）" <br/> |
| whenTrue    | 此输入是一个[计算字段](#evaluated-fields)，表示条件计算为*true*时要返回的值。 常量字符串应以单引号（"and"）返回。 <br/>示例值： <br/> "=" 协定 ""<br/>"= $ （/document/contractType）" <br/> "= $ （/document/entities/\*）" <br/> |
| whenFalse   | 此输入是一个[计算字段](#evaluated-fields)，表示条件计算为*false*时要返回的值。 <br/>示例值： <br/> "=" 协定 ""<br/>"= $ （/document/contractType）" <br/> "= $ （/document/entities/\*）" <br/>

## <a name="skill-outputs"></a>技能输出
有一个简单的输出，就是所谓的 "输出"。 如果条件为 false，则返回值*whenFalse* ; 如果条件为 true，则返回*whenTrue* 。

## <a name="examples"></a>示例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>示例技能定义1：筛选文档以仅返回法语文档

如果文档的语言为法语，则以下输出返回一个句子数组（"/document/frenchSentences"）。 如果语言不是法语，则将值设置为*null*。

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
如果将 "/document/frenchSentences" 用作其他技能的*上下文*，则只有在 "/document/frenchSentences" 未设置为*null*时才会运行该技能。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>示例技能定义2：为不存在的值设置默认值

下面的输出将创建一个设置为文档语言的批注（"/document/languageWithDefault"），如果未设置该语言，则为 "es"。

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>示例技能定义3：将两个字段中的值合并为一个字段

在此示例中，某些句子具有*frenchSentiment*属性。 只要*frenchSentiment*属性为 null，就需要使用*englishSentiment*值。 我们将输出分配给名为*情绪*的成员（"/document/sentiment/*/sentiment"）。

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>示例技能定义4：对单个字段的数据转换

在此示例中，我们收到0到1之间的*情绪*。 我们想要将它转换为介于-1 和1之间的。 我们可以使用条件技能来执行此次要转换。

在此示例中，我们不会使用技能的条件性方面，因为条件始终*为 true*。

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
计算一些参数，因此需要特别注意遵循记录的模式。 表达式必须以等号开头。 路径必须以 "$ （" 和 "）" 分隔。 请确保用单引号将字符串括起来。 这有助于计算器区分字符串和实际路径和运算符。 此外，请确保在运算符周围放置空格（例如，路径中的 "*" 表示除乘法以外的其他内容）。


## <a name="next-steps"></a>后续步骤

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
