---
title: 将 JSON 和 XML 转换为液体模板
description: 在 Azure 逻辑应用中使用液体模板作为地图来转换 JSON 和 XML
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: b3919cbbe0ba7a796a21ae566afb8e2d9fa784db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716667"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用液体模板转换 JSON 和 XML

若要在逻辑应用中执行基本 JSON 转换，可以使用本机 [数据操作](../logic-apps/logic-apps-perform-data-operations.md) ，例如 **撰写** 或 **分析 JSON**。 对于包含迭代、控制流和变量等元素的高级和复杂 JSON 到 JSON 转换，请使用 [液体](https://shopify.github.io/liquid/) 开源模板语言创建和使用描述这些转换的模板。 你还可以 [执行其他转换](#other-transformations)，例如，JSON 到文本、XML 到 JSON 和 xml 到文本。

在逻辑应用中执行液体转换之前，必须先创建一个用于定义所需映射的液体模板。 然后，将 [模板作为地图上传](../logic-apps/logic-apps-enterprise-integration-maps.md) 到 [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 向逻辑应用添加 "将 **Json 转换为 json-液体** " 操作时，可以选择液体模板作为要使用的操作的地图。

本文介绍如何完成以下任务：

* 创建液体模板。
* 将模板添加到集成帐户。
* 将液体转换操作添加到逻辑应用。
* 选择要使用的映射模板。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 关于 [Liquid 模板语言](https://shopify.github.io/liquid/)的基本知识

  > [!NOTE]
  > 将 **Json 转换为 json-液体** 操作遵循 [液体的 DotLiquid 实现](https://github.com/dotliquid/dotliquid)，这不同于来自 [Shopify 实现的](https://shopify.github.io/liquid)特定情况。 有关详细信息，请参阅 [液体模板注意事项](#liquid-template-considerations)。

## <a name="create-the-template"></a>创建模板

1. 创建用作 JSON 转换的地图的液体模板。 您可以使用任何所需的编辑工具。

   对于本示例，请创建此部分中所述的示例液体模板：

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. 使用扩展名保存模板 `.liquid` 。 本示例使用 `SimpleJsonToJsonTemplate.liquid`。

## <a name="upload-the-template"></a>上载模板

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入 `integration accounts` ，然后选择 " **集成帐户**"。

   ![查找“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 找到并选择你的集成帐户。

   ![选择“集成帐户”](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. 在“概览”窗格的“组件”下，选择“映射”。

    ![选择“映射”磁贴](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. 在“映射”窗格上选择“添加”，并为映射提供以下详细信息：

   | 属性 | “值” | 说明 |
   |----------|-------|-------------|
   | **名称** | `JsonToJsonTemplate` | 映射的名称，在此示例中为“JsonToJsonTemplate” |
   | **映射类型** | **liquid** | 你的映射的类型。 对于 JSON 到 JSON 转换，必须选择“liquid”。 |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | 用于转换的现有 Liquid 模板或映射文件，在此示例中为“SimpleJsonToJsonTemplate.liquid”。 若要查找此文件，可使用文件选取器。 有关映射大小限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)。 |
   |||

   ![添加 Liquid 模板](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>添加液体转换操作

1. 在 Azure 门户中，执行以下步骤[创建空的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在逻辑应用设计器中，向逻辑应用中添加[请求触发器](../connectors/connectors-native-reqres.md#add-request)。

1. 在触发器下，选择“新建步骤”。 在搜索框中，输入 `liquid` 作为筛选器，然后选择以下操作：**将 JSON 转换为 JSON - Liquid**

   ![查找并选择 Liquid 操作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. 打开“映射”列表，选择 Liquid 模板，即此示例中的“JsonToJsonTemplate”。

   ![选择映射](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果 "映射" 列表为空，则很可能是因为逻辑应用未链接到集成帐户。 
   要将逻辑应用链接到具有 Liquid 模板或映射的集成帐户，请执行以下步骤：

   1. 在逻辑应用菜单中选择“工作流设置”。

   1. 从“选择集成帐户”列表中选择集成帐户，并选择“保存” 。

      ![将逻辑应用链接到集成帐户](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 现在，将 Content 属性添加到此操作。 打开“添加新参数”列表，选择“Content”。 

   ![向操作添加“Content”属性](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. 若要设置“Content”属性值，请在“内容”框中单击，以便显示动态内容列表。 选择“Body”标记（表示从触发器输出的正文内容）。

   ![为“Content”属性值选择“Body”标记](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   完成后，此操作如以下示例所示：

   ![已完成“将 JSON 转换为 JSON”操作](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>测试逻辑应用

通过使用 [Postman](https://www.getpostman.com/postman) 或类似的工具，将 JSON 输入发布到逻辑应用。 逻辑应用转换后的 JSON 输出如此示例中所示：

![示例输出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>液体模板注意事项

* 液体模板遵循 Azure 逻辑应用中的 [地图的文件大小限制](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) 。

* 将 **Json 转换为 json-液体** 操作遵循 [液体的 DotLiquid 实现](https://github.com/dotliquid/dotliquid)。 此实现是从 [液体 Shopify 实现](https://shopify.github.io/liquid/) 到 .NET Framework 的端口，在 [特定情况下](https://github.com/dotliquid/dotliquid/issues)有所不同。

  下面是已知的区别：

  * 将 **Json 转换为 json-液体** 操作会以本机方式输出字符串，其中可能包括 JSON、XML、HTML 等。 液体操作仅指示来自液体模板的预期文本输出为 JSON 字符串。 操作指示逻辑应用将输入分析为 JSON 对象，并应用包装器，使液体可以解释 JSON 结构。 转换后，操作将指示逻辑应用分析从液体返回到 JSON 的文本输出。

    DotLiquid 不能以本机方式了解 JSON，因此请确保将反斜杠字符转义 (`\`) 和其他保留的 JSON 字符。

  * 如果模板使用[液体筛选器](https://shopify.github.io/liquid/basics/introduction/#filters)，请确保遵循使用*句子大小写*的[DotLiquid 和 c # 命名约定](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)。 对于所有液体转换，请确保模板中的筛选器名称也使用句子大小写。 否则，筛选器将不起作用。

    例如，使用筛选器时， `replace` `Replace` 请使用，而不是 `replace` 。 如果在 [DotLiquid online](http://dotliquidmarkup.org/try-online)上尝试示例，则会应用相同的规则。 有关详细信息，请参阅 [Shopify 液体滤波器](https://shopify.dev/docs/themes/liquid/reference/filters) 和 [DotLiquid 液滤波器](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)。 Shopify 规范包含每个筛选器的示例，因此，为了进行比较，可以在 DotLiquid 尝试这些示例 [-请在联机](https://dotliquidmarkup.org/try-online)时尝试。

  * `json`Shopify 扩展筛选器中的筛选器当前[未在 DotLiquid 中实现](https://github.com/dotliquid/dotliquid/issues/384)。 通常，您可以使用此筛选器来准备用于 JSON 字符串分析的文本输出，但您需要改用 `Replace` 筛选器。

  * `Replace` [DotLiquid 实现](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425)中的标准筛选器使用[正则表达式 (RegEx) 匹配](/dotnet/standard/base-types/regular-expression-language-quick-reference)，而[Shopify 实现](https://shopify.github.io/liquid/filters/replace/)使用[简单字符串匹配](https://github.com/Shopify/liquid/issues/202)。 这两种实现看起来是以相同的方式工作，直到在 match 参数中使用 RegEx 保留字符或转义符。

    例如，若要将正则表达式转义反斜杠 (`\`) 转义字符，请使用 `| Replace: '\\', '\\'` ，而不是 `| Replace: '\', '\\'` 。 这些示例说明了 `Replace` 当你尝试对反斜杠字符进行转义时，筛选器的行为方式有所不同。 尽管此版本的运行成功：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    结果如下：

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    此版本失败：

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    出现此错误：

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    有关详细信息，请参阅 " [替换标准筛选器使用正则表达式模式匹配 ...](https://github.com/dotliquid/dotliquid/issues/385)"。

  * `Sort` [DotLiquid 实现](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)中的筛选器按属性对数组或集合中的项进行排序，但存在以下差异：<p>

    * 遵循 [Shopify 的 sort_natural 行为](https://shopify.github.io/liquid/filters/sort_natural/)，而不 [是 Shopify 的排序行为](https://shopify.github.io/liquid/filters/sort/)。

    * 仅按字符串字母数字顺序排序。 有关详细信息，请参阅 [数字排序](https://github.com/Shopify/liquid/issues/980)。

    * 使用不区分 *大小* 写的顺序，不区分大小写。 有关详细信息，请参阅 [排序筛选器不遵循 Shopify 规范中的大小写行为]( https://github.com/dotliquid/dotliquid/issues/393)。

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>使用液体的其他转换

液体并不局限于 JSON 转换。 你还可以使用液体来执行其他转换，例如：

* [JSON 到文本](#json-text)
* [XML 到 JSON](#xml-json)
* [XML 到文本](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>将 JSON 转换为文本

下面是此示例中使用的液体模板：

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

下面是输入和输出示例：

![将 JSON 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>将 XML 转换为 JSON

下面是此示例中使用的液体模板：

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`循环是用于 XML 输入的自定义循环机制，以便您可以创建可避免尾部逗号的 JSON 有效负载。 此外， `where` 此自定义循环机制的条件使用 XML 元素的名称进行比较，而不是像其他液体过滤器那样使用元素的值。 有关详细信息，请参阅 [设置正文策略深入](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)了解。

下面是输入和输出示例：

![将 XML 输出为 JSON 的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>将 XML 转换为文本

下面是此示例中使用的液体模板：

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

下面是输入和输出示例：

![将 XML 输出为文本的示例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>后续步骤

* [Shopify 液体语言和示例](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-尝试联机](https://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub 问题](https://github.com/dotliquid/dotliquid/issues/)
* 了解有关[地图](../logic-apps/logic-apps-enterprise-integration-maps.md)的详细信息
