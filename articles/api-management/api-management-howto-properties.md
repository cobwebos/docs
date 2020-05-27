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
ms.openlocfilehash: dc8ca7296658f4113d86765f230ca0158727255f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649200"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>如何在 Azure API 管理策略中使用命名值

API 管理策略是一项强大的系统功能，允许 Azure 门户通过配置更改 API 的行为。 策略是一组语句，在请求或响应 API 时按顺序执行。 可以使用文字文本值、策略表达式和命名值构造策略语句。

每个 API 管理服务实例都有一组键/值对（称为“命名值”），它们是服务实例的全局值。 没有对集合中的项数施加限制。 命名值可以用来管理所有 API 配置和策略的常量字符串值。 每个命名值可能有以下属性：

| 属性      | 类型            | 说明                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | 用于在策略中引用命名值。 1 到 256 个字符的字符串。 只允许字母、数字、点和短划线。 |
| `Value`        | string          | 实际值。 不得为空或只由空格组成。 最多 4096 个字符长。                                        |
| `Secret`       | boolean         | 确定值是否为密钥以及是否应加密。                                                               |
| `Tags`         | 字符串数组 | 用于筛选命名值列表。 最多 32 个标记。                                                                                    |

![命名值](./media/api-management-howto-properties/named-values.png)

命名值可以包含文本字符串和[策略表达式](/azure/api-management/api-management-policy-expressions)。 例如，`Expression` 的值是一个策略表达式，其返回的字符串包含当前日期和时间。 命名值 `Credential` 被标记为机密，因此默认情况下未显示其值。

| 名称       | Value                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False  | vital-numbers |
| 凭据 | ••••••••••••••••••••••     | True   | security      |
| 表达式 | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> 可以使用 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)服务中存储的值，而不是 API 管理服务中存储的命名值，如此[示例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)所示。

## <a name="to-add-and-edit-a-named-value"></a>添加和编辑命名值

![添加命名值](./media/api-management-howto-properties/add-property.png)

1. 在“API 管理”下面选择“API”。  
2. 选择“命名值”  。
3. 按“+添加”  。

    “名称”和“值”是必需值。 如果值为机密，请选中“这是机密”复选框。  输入一个或多个用于组织命名值的可选标记，并单击“保存”。

4. 单击**创建**。

创建命名值后，可以通过单击该值对其进行编辑。 如果更改了命名值的名称，则会自动更新引用了该命名值的策略，让其使用新名称。

## <a name="to-delete-a-named-value"></a>删除命名值

若要删除某个命名值，请单击其旁边的“删除”。 

> [!IMPORTANT]
> 如果有策略引用了该命名值，则无法成功地将它删除，除非将它从所有使用它的策略中删除。

## <a name="to-search-and-filter-named-values"></a>搜索和筛选命名值

“命名值”选项卡包含搜索和筛选功能，用于管理命名值。  若要按名称筛选命名值列表，请在“搜索属性”文本框中输入搜索词。  若要显示所有命名值，请清除“搜索属性”文本框，并按 Enter。 

若要按标记筛选列表，请将一个或多个标记输入到“按标记筛选”文本框中。  若要显示所有命名值，请清除“按标记筛选”文本框，并按 Enter。 

## <a name="to-use-a-named-value"></a>使用命名值

若要在策略中使用某个命名值，请将其名称包含在双大括号中（例如 `{{ContosoHeader}}`），如以下示例所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此示例中，`ContosoHeader` 用作 `set-header` 策略中一个标头的名称，`ContosoHeaderValue` 用作该标头的值。 在请求或响应 API 管理网关的过程中，如果要对该策略进行评估，则会将 `{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 替换为各自的值。

命名值可以用作完整的特性或元素值（如前面的示例所示），但也可插入到文字文本表达式中或与该表达式的一部分组合在一起，如以下示例所示：`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

命名值还可以包含策略表达式。 以下示例使用了 `ExpressionProperty`。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

评估此策略时，会将 `{{ExpressionProperty}}` 替换为其值：`@(DateTime.Now.ToString())`。 由于该值是一个策略表达式，因此会对表达式进行评估并且策略将继续执行。

若要在开发人员门户中对此进行测试，可调用一个其策略命名值处于范围内的操作。 在以下示例中，调用了一个包含两个带命名值的前述示例性 `set-header` 策略的操作。 请注意，响应包含两个自定义标头，这两个标头是使用带命名值的策略配置的。

![开发人员门户][api-management-send-results]

如果在 [API 检查器跟踪](api-management-howto-api-inspector.md)中查看包含两个带命名值的前述示例性策略的调用，则可以看到两个 `set-header` 策略，这两个策略已插入了命名值，并已针对包含策略表达式的命名值进行了策略表达式计算。

![API 检查器跟踪][api-management-api-inspector-trace]

虽然命名值可以包含策略表达式，但不能包含其他命名值。 如果将包含命名值引用的文本用作值（例如 `Text: {{MyProperty}}`），将不会解析和替换该引用。

## <a name="next-steps"></a>后续步骤

-   详细了解如何使用策略
    -   [API 管理中的策略](api-management-howto-policies.md)
    -   [策略参考](/azure/api-management/api-management-policies)
    -   [策略表达式](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
