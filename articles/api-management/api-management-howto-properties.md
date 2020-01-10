---
title: 如何在 Azure API 管理策略中使用命名值
description: 了解如何在 Azure API 管理策略中使用命名值。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 7c25455e28e57ff40664a69718a2e406b52b7632
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834291"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>如何在 Azure API 管理策略中使用命名值

API 管理策略是一项强大的系统功能，允许 Azure 门户通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 可以使用文字文本值、策略表达式和命名值构造策略语句。

每个 API 管理服务实例都有一个名为 "命名值" 的键/值对集合，这些键/值对是服务实例的全局。 集合中的项数没有施加限制。 命名值可用于管理所有 API 配置和策略的常量字符串值。 每个命名值都可能具有以下特性：

| Attribute      | 类型            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | 字符串          | 用于在策略中引用命名值。 一个到256个字符的字符串。 只允许使用字母、数字、点和短划线。 |
| `Value`        | 字符串          | 实际值。 不得为空或仅包含空格。 最大长度为4096个字符。                                        |
| `Secret`       | boolean         | 确定值是否为密钥以及是否应加密。                                                               |
| `Tags`         | 字符串数组 | 用于筛选命名值列表。 最多32个标记。                                                                                    |

![命名值](./media/api-management-howto-properties/named-values.png)

命名值可以包含文本字符串和[策略表达式](/azure/api-management/api-management-policy-expressions)。 例如，`Expression` 的值是一个策略表达式，其返回的字符串包含当前日期和时间。 `Credential` 的命名值被标记为机密，因此默认情况下不显示其值。

| 名称       | 值                      | 机密 | 标记          |
| ---------- | -------------------------- | ------ | ------------- |
| 值      | 42                         | 错误  | 重要-数字 |
| 凭据 | ••••••••••••••••••••••     | 正确   | 安全性      |
| Expression | @(DateTime.Now.ToString()) | 错误  |               |

> [!NOTE]
> 如本[示例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)所示，可以使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)服务中存储的值，而不是存储在 API 管理服务中的命名值。

## <a name="to-add-and-edit-a-named-value"></a>添加和编辑命名值

![添加命名值](./media/api-management-howto-properties/add-property.png)

1. 在“API 管理”下面选择“API”。
2. 选择“命名值”。
3. 按“+添加”。

    “名称”和“值”是必需值。 如果值为机密，请选中 "_这是机密_" 复选框。 输入一个或多个用于组织命名值的可选标记，并单击“保存”。

4. 单击“创建”。

创建命名值后，可以通过单击它来对其进行编辑。 如果更改命名值名称，则引用该命名值的任何策略都将自动更新为使用新名称。

有关使用 REST API 编辑命名值的信息，请参阅[使用 REST API 编辑命名值](/rest/api/apimanagement/2019-01-01/property?patch)。

## <a name="to-delete-a-named-value"></a>删除命名值

若要删除命名值，请单击要删除的命名值旁边的 "**删除**"。

> [!IMPORTANT]
> 如果指定的值由任何策略引用，则在从使用该命名值的所有策略中删除该命名值之前，您将无法成功删除该命名值。

有关使用 REST API 删除命名值的信息，请参阅[使用 REST API 删除命名值](/rest/api/apimanagement/2019-01-01/property/delete)。

## <a name="to-search-and-filter-named-values"></a>搜索和筛选命名值

“命名值”选项卡包含搜索和筛选功能，用于管理命名值。 若要按名称筛选命名值列表，请在 "**搜索属性**" 文本框中输入搜索词。 若要显示所有命名值，请清除“搜索属性”文本框，并按 Enter。

若要按标记筛选列表，请在 "**按标记筛选**" 文本框中输入一个或多个标记。 若要显示所有命名值，请清除“按标记筛选”文本框，并按 Enter。

## <a name="to-use-a-named-value"></a>使用命名值

若要在策略中使用已命名的值，请将其名称放在双向大括号中，如 `{{ContosoHeader}}`，如以下示例中所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此示例中，`ContosoHeader` 用作 `set-header` 策略中一个标头的名称，`ContosoHeaderValue` 用作该标头的值。 在请求或响应 API 管理网关的过程中评估此策略时，`{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 会替换为各自的值。

命名值可以用作完整的特性或元素值（如前面的示例所示），但也可插入到文字文本表达式中或与该表达式的一部分组合在一起，如以下示例所示：`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

命名值还可以包含策略表达式。 以下示例使用 `ExpressionProperty`。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

评估此策略时，会将 `{{ExpressionProperty}}` 替换为其值：`@(DateTime.Now.ToString())`。 由于值为策略表达式，因此会计算该表达式，然后策略会继续执行相关操作。

若要在开发人员门户中对此进行测试，可调用一个其策略命名值处于范围内的操作。 在以下示例中，调用了一个包含两个带命名值的前述示例性 `set-header` 策略的操作。 请注意，响应包含两个自定义标头，这两个标头是使用带命名值的策略配置的。

![开发人员门户][api-management-send-results]

如果在[API 检查器跟踪](api-management-howto-api-inspector.md)中查看包含两个前面带有命名值的示例策略的调用，则可以看到两个 `set-header` 策略，其中包含已命名的值，以及包含策略表达式的命名值的策略表达式计算。

![API 检查器跟踪][api-management-api-inspector-trace]

虽然命名值可以包含策略表达式，但不能包含其他命名值。 如果包含命名值引用的文本用于某个值（例如 `Text: {{MyProperty}}`），则不会解析和替换该引用。

## <a name="next-steps"></a>后续步骤

-   详细了解如何使用策略
    -   [API 管理中的策略](api-management-howto-policies.md)
    -   [策略参考](/azure/api-management/api-management-policies)
    -   [策略表达式](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
