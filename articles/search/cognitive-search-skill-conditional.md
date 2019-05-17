---
title: 条件性的认知搜索技能 （Azure 搜索） |Microsoft Docs
description: 条件的技能，可以筛选、 创建默认值，并将值合并。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791519"
---
#   <a name="conditional-skill"></a>条件的技能

*条件技能*支持要求的布尔操作以确定要分配给输出的数据的 Azure 搜索方案。 这些方案包括筛选、 将分配默认值，以及合并基于条件的数据。

下面的伪代码演示了条件技能完成：

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 此技术没有绑定到 Azure 认知服务 API，并不需要支付使用它。 但是，您应仍然[附加认知服务资源](cognitive-search-attach-cognitive-services.md)重写限制为很小的每日到你的"免费"资源选项。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>计算的字段

此技术很特别，因为其输入为计算的字段。

以下各项是表达式的有效的值：

-   批注路径 (在表达式中的路径必须分隔"$("and")")
 <br/>
    示例：
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  文本 （字符串、 数字、 true、 false、 null） <br/>
    示例：
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  使用比较运算符的表达式 (= =、 ！ =、 > =、 >、 < =、 <) <br/>
    示例：
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   使用布尔运算符的表达式 (& &、 | |，！，^) <br/>
    示例：
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   使用数字运算符的表达式 (+、-， \*、 /、 %) <br/>
    示例： 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

由于条件的技能支持评估，因此可以在次要转换方案中使用它。 有关示例，请参阅[技能定义 4](#transformation-example)。

## <a name="skill-inputs"></a>技能输入
输入区分大小写。

| 输入   | 描述 |
|-------------|-------------|
| condition   | 此输入是[计算字段](#evaluated-fields)，表示要计算的条件。 此条件的计算结果应为布尔值 (*，则返回 true*或*false*)。   <br/>  示例： <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 此输入是[计算字段](#evaluated-fields)，表示要返回该条件的计算结果为的值*true*。 常量字符串应返回在单引号 （' 和'）。 <br/>示例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 此输入是[计算字段](#evaluated-fields)，表示要返回该条件的计算结果为的值*false*。 <br/>示例值： <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>技能输出
没有单个输出，只需调用"output"。 它将返回值*whenFalse*如果条件为 false 或*whenTrue*如果条件为 true。

## <a name="examples"></a>示例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>示例技能定义 1:筛选文档返回仅法语文档

如果文档的语言为法语，下面的输出返回数组的句子 ("/ 文档/frenchSentences")。 如果语言不是法语，将值设置为*null*。

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
如果"文档/frenchSentences"用作*上下文*的另一项技巧，该技能将仅运行"文档/frenchSentences"未设置为*null*。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>示例技能定义 2:设置一个值，不存在的默认值

下面的输出创建的如果未设置语言设置为文档的语言或"es"的批注 ("/ 文档/languageWithDefault")。

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>示例技能定义 3:将从两个字段的值合并为一个

在此示例中，有一些句子*frenchSentiment*属性。 每当*frenchSentiment*属性为 null，我们想要使用*englishSentiment*值。 我们将输出分配给成员称为*情绪*("/ 记录/情绪 / * / 情绪")。

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>示例技能定义 4:在单个字段的数据转换

在此示例中，我们收到*情绪*这就是介于 0 和 1 之间。 我们想要转换为-1 和 1 之间。 我们可以使用条件性技能来执行此次要转换。

在此示例中，我们不使用的条件性方面的技能，因为该条件始终 *，则返回 true*。

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
某些参数被计算，因此您需要时务必小心，遵循有案可稽的模式。 表达式必须以等号开头。 路径必须分隔"$("and")"。 请确保将字符串放在单引号内。 这有助于区分字符串和实际路径和运算符，计算器。 此外，请确保将运算符周围的空白区域 (例如，"*"路径中的含义不同于 multiply)。


## <a name="next-steps"></a>后续步骤

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
