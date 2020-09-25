---
title: 阻止特定 API 连接器的连接
description: 限制在 Azure 逻辑应用中创建和使用 API 连接
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: bd6afa8b3776ed48d4b25a36b2902265fa0ab5c4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269754"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>阻止 Azure 逻辑应用中的连接器创建的连接

如果你的组织不允许在 Azure 逻辑应用中使用相关连接器连接到受限的或未批准的资源，则可阻止在逻辑应用工作流中创建和使用这些连接的功能。 可以使用 [Azure Policy](../governance/policy/overview.md) 定义并强制实施[策略](../governance/policy/overview.md#policy-definition)来阻止创建或使用你要阻止的连接器的连接。 例如，出于安全原因，你可能想要阻止与特定社交媒体平台或其他服务和系统的连接。

本主题展示了如何使用 Azure 门户设置一个阻止特定连接的策略，但你还可以通过其他方式（例如，通过 Azure REST API、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板）创建策略定义。 有关详细信息，请参阅[教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前 [创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/) 。

* 要阻止的连接器的引用 ID。 有关详细信息，请参阅[查找连接器引用 ID](#connector-reference-ID)。

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>查找连接器引用 ID

如果已经有一个具有要阻止的连接的逻辑应用，请按照[适用于 Azure 门户的步骤](#connector-ID-portal)操作。 否则，请执行以下步骤：

1. 访问[逻辑应用连接器列表](/connectors/connector-reference/connector-reference-logicapps-connectors)。

1. 找到要阻止的连接器的参考页。

   例如，如果想要阻止已弃用的 Instagram 连接器，请参阅此页：

   `https://docs.microsoft.com/connectors/instagram/`

1. 在该页的 URL 中，复制并保存末尾处的连接器引用 ID，不带正斜杠 (`/`)，例如 `instagram`。

   稍后，在创建策略定义时在定义的条件语句中将使用此 ID，例如：

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的逻辑应用。

1. 在逻辑应用菜单上，选择“逻辑应用代码视图”，以便查看逻辑应用的 JSON 定义。

   ![打开“逻辑应用代码视图”，查找连接器 ID](./media/block-connections-connectors/code-view-connector-id.png)

1. 查找包含 `$connections` 对象的 `parameters` 对象，其中包括逻辑应用中每个连接的 `{connection-name}` 对象并指定了有关该连接的信息：

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   例如，对于 Instagram 连接器，查找用于 `instagram` 标识 Instagram 连接的对象：

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. 对于要阻止的连接，请按以下格式查找 `id` 属性和值： 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   例如，以下是 `id` Instagram 连接的属性和值：

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. 在 `id` 属性值中，复制并保存末尾处的连接器引用 ID，例如 `instagram`。

   稍后，在创建策略定义时在定义的条件语句中将使用此 ID，例如：

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>创建策略以阻止创建连接

若要完全阻止在逻辑应用中创建连接，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 在门户的搜索框中输入 `policy`，然后选择“策略”。

   ![在 Azure 门户中，找到并选择“策略”](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在“策略”菜单上，在“创作”下，选择“定义” > “+ 策略定义”。

   ![选择“定义”>“+ 策略定义”](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在“策略定义”下，根据该示例下所述的属性，提供策略定义的信息：

   ![显示 "策略定义" 属性的屏幕截图。](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | 属性 | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **定义位置** | 是 | <*Azure-subscription-name*> | 用于策略定义的 Azure 订阅 <p><p>1.若要查找你的订阅，请选择省略号 ( **...** ) 按钮。 <br>2.在“订阅”列表中找到并选择你的订阅。 <br>3.完成后，选择“选择”。 |
   | **名称** | 是 | <policy-definition-name> | 用于策略定义的名称 |
   | **说明** | 否 | <policy-definition-name> | 策略定义的说明 |
   | **类别** | 是 | **逻辑应用** | 策略定义的现有类别或新类别的名称 |
   | **策略强制执行** | 是 | **Enabled** | 此设置指定在保存工作时是启用还是禁用策略定义。 |
   ||||

1. 在“策略规则”下，JSON 编辑框已使用策略定义模板预先填充。 根据下表中所述的属性，按照以下语法将此模板替换为你的[策略定义](../governance/policy/concepts/definition-structure.md)：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 属性 | Value | 说明 |
   |----------|-------|-------------|
   | `mode` | `All` | 一个模式，用于确定策略所评估的资源类型。 <p><p>此方案将 `mode` 设置为 `All`，这会将策略应用于 Azure 资源组、订阅和所有资源类型。 <p><p>有关详细信息，请参阅[策略定义结构 - 模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 一个条件，用于确定何时强制实施策略规则 <p><p>在这种情况下，`{condition-to-evaluate}` 确定 `Microsoft.Web/connections/api.id` 中的 `api.id` 值是否与指定了通配符 (*) 值的 `*managedApis/{connector-name}` 匹配。 <p><p>有关详细信息，请参阅[策略定义结构 - 策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `field` | `Microsoft.Web/connections/api.id` | 要与条件进行比较的 `field` 值 <p><p>在这种情况下，`field` 使用[别名](../governance/policy/concepts/definition-structure.md#aliases) `Microsoft.Web/connections/api.id` 来访问连接器属性 `api.id` 中的值。 |
   | `like` | `*managedApis/{connector-name}` | 用于比较 `field` 值的逻辑运算符和值 <p><p>在这种情况下，`like` 运算符和通配符 (*) 字符都确保规则的工作方式与区域无关，而字符串 `*managedApis/{connector-name}` 是要匹配的值，其中，`{connector-name}` 是要阻止的连接器的 ID。 <p><p>例如，假设要阻止创建到社交媒体平台或数据库的连接： <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>- SQL Server 或 Azure SQL：`sql` <p><p>若要查找这些连接器 ID，请参阅本主题上文中的[查找连接器引用 ID](#connector-reference-ID)。 |
   | `then` | `{effect-to-apply}` | 满足 `if` 条件时要应用的效果 <p><p>在这种情况下，`{effect-to-apply}` 会阻止不符合策略的请求或操作并让其失败。 <p><p>有关详细信息，请参阅[策略定义结构 - 策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect` 将阻止请求，即阻止创建指定的连接 <p><p>有关详细信息，请参阅[了解 Azure Policy 效果 - 拒绝](../governance/policy/concepts/effects.md#deny)。 |
   ||||

   例如，假设要阻止创建与 Instagram 连接器的连接。 下面是可以使用的策略定义：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   下面是“策略规则”框的显示方式：

   ![显示带有策略规则示例的 "策略规则" 框的屏幕截图。](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   对于多个连接器，可以添加更多条件，例如：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. 完成后，选择“保存”。 保存策略定义后，Azure Policy 会生成更多的属性值并将其添加到策略定义。

1. 接下来，若要分配需要在其中强制实施策略的策略定义，请[创建策略分配](#create-policy-assignment)。

有关 Azure 策略定义的详细信息，请参阅以下主题：

* [策略结构定义](../governance/policy/concepts/definition-structure.md)
* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
* [Azure 逻辑应用的 Azure Policy 内置策略定义](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>创建策略以阻止使用连接

在逻辑应用中创建连接时，该连接作为单独的 Azure 资源存在。 如果仅删除逻辑应用，则连接不会自动删除，在被删除之前会一直存在。 你可能会遇到这样的情况：连接已存在，或者必须创建在逻辑应用外部使用的连接。 你仍然可以阻止在逻辑应用中使用现有连接的功能，方法是：创建一个策略来阻止保存具有受限或未批准连接的逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在门户的搜索框中输入 `policy`，然后选择“策略”。

   ![在 Azure 门户中，找到并选择“策略”](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在“策略”菜单上，在“创作”下，选择“定义” > “+ 策略定义”。

   ![选择“定义”>“+ 策略定义”](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在 " **策略定义**" 下，根据示例中描述的属性提供策略定义的信息，并使用 Instagram 作为示例继续操作：

   ![策略定义属性](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | 属性 | 必选 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **定义位置** | 是 | <*Azure-subscription-name*> | 用于策略定义的 Azure 订阅 <p><p>1.若要查找你的订阅，请选择省略号 ( **...** ) 按钮。 <br>2.在“订阅”列表中找到并选择你的订阅。 <br>3.完成后，选择“选择”。 |
   | **名称** | 是 | <policy-definition-name> | 用于策略定义的名称 |
   | **说明** | 否 | <policy-definition-name> | 策略定义的说明 |
   | **类别** | 是 | **逻辑应用** | 策略定义的现有类别或新类别的名称 |
   | **策略强制执行** | 是 | **Enabled** | 此设置指定在保存工作时是启用还是禁用策略定义。 |
   ||||

1. 在“策略规则”下，JSON 编辑框已使用策略定义模板预先填充。 根据下表中所述的属性，按照以下语法将此模板替换为你的[策略定义](../governance/policy/concepts/definition-structure.md)：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 属性 | Value | 说明 |
   |----------|-------|-------------|
   | `mode` | `All` | 一个模式，用于确定策略所评估的资源类型。 <p><p>在此情况下会将 `mode` 设置为 `All`，这会将策略应用于 Azure 资源组、订阅和所有资源类型。 <p><p>有关详细信息，请参阅[策略定义结构 - 模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 一个条件，用于确定何时强制实施策略规则 <p><p>在此情况下，`{condition-to-evaluate}` 确定 `[string(field('Microsoft.Logic/workflows/parameters'))]` 的字符串输出是否包含字符串 `{connector-name}`。 <p><p>有关详细信息，请参阅[策略定义结构 - 策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 要与条件进行比较的值 <p><p>在此情况下，`value` 是 `[string(field('Microsoft.Logic/workflows/parameters'))]` 的字符串输出，它将 `Microsoft.Logic/workflows/parameters` 对象中的 `$connectors` 对象转换为字符串。 |
   | `contains` | `{connector-name}` | 用于与 `value` 属性进行比较的逻辑运算符和值 <p><p>在此情况下，`contains` 运算符确保规则正常工作，而不管 `{connector-name}` 出现在何处，其中，字符串 `{connector-name}` 是要限制或阻止的连接器的 ID。 <p><p>例如，假设要阻止使用连接到社交媒体平台或数据库： <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>- SQL Server 或 Azure SQL：`sql` <p><p>若要查找这些连接器 ID，请参阅本主题上文中的[查找连接器引用 ID](#connector-reference-ID)。 |
   | `then` | `{effect-to-apply}` | 满足 `if` 条件时要应用的效果 <p><p>在这种情况下，`{effect-to-apply}` 会阻止不符合策略的请求或操作并让其失败。 <p><p>有关详细信息，请参阅[策略定义结构 - 策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect` 将`deny`或阻止请求，即阻止保存使用指定连接的逻辑应用 <p><p>有关详细信息，请参阅[了解 Azure Policy 效果 - 拒绝](../governance/policy/concepts/effects.md#deny)。 |
   ||||

   例如，假设要阻止保存使用 Instagram 连接的逻辑应用。 下面是可以使用的策略定义：

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   下面是“策略规则”框的显示方式：

   ![策略定义规则](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 完成后，选择“保存”。 保存策略定义后，Azure Policy 会生成更多的属性值并将其添加到策略定义。

1. 接下来，若要分配需要在其中强制实施策略的策略定义，请[创建策略分配](#create-policy-assignment)。

有关 Azure 策略定义的详细信息，请参阅以下主题：

* [策略结构定义](../governance/policy/concepts/definition-structure.md)
* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
* [Azure 逻辑应用的 Azure Policy 内置策略定义](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>创建策略分配

接下来，你需要分配要在其中强制实施策略的策略定义，例如，将其分配给单个资源组、多个资源组、Azure Active Directory (Azure AD) 租户或 Azure 订阅。 对于此任务，请按照以下步骤创建策略分配：

1. 如果你已注销，请重新登录到 [Azure 门户](https://portal.azure.com)。 在门户的搜索框中输入 `policy`，然后选择“策略”。

   ![在 Azure 门户中找到并选择“策略”](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在“策略”菜单上，在“创作”下，选择“分配” > “分配策略”。

   ![选择“分配”>“分配”](./media/block-connections-connectors/add-new-policy-assignment.png)

1. 在“基本信息”下，为策略分配提供以下信息：

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **范围** | 是 | 要在其中强制实施策略分配的资源。 <p><p>1.在“作用域”框旁边，选择省略号 ( **...** ) 按钮。 <br>2.在“订阅”列表中选择 Azure 订阅。 <br>3.（可选）在“资源组”列表中选择资源组。 <br>4.完成后，选择“选择”。 |
   | **排除项** | 否 | 要从策略分配中排除的任何 Azure 资源。 <p><p>1.在“排除项”框旁边，选择省略号 ( **...** ) 按钮。 <br>2.从“资源”列表中选择相应资源 >“添加到所选作用域”。 <br>3.完成后，选择“保存”。 |
   | **策略定义** | 是 | 要分配和强制实施的策略定义的名称。 此示例将继续执行示例 Instagram 策略 "阻止 Instagram 连接"。 <p><p>1.在“策略定义”框旁边，选择省略号 ( **...** ) 按钮。 <br>2.使用“类型”筛选器或“搜索”框找到并选择策略定义。 <br>3.完成后，选择“选择”。 |
   | **分配名称** | 是 | 用于策略分配的名称（如果不同于策略定义） |
   | **分配 ID** | 是 | 为策略分配自动生成的 ID |
   | **说明** | 否 | 策略分配的说明 |
   | **策略强制执行** | 是 | 用于启用或禁用策略分配的设置 |
   | **分配者** | 否 | 创建并应用了策略分配的人员的姓名 |
   ||||

   例如，使用 Instagram 示例将策略分配给 Azure 资源组：

   ![策略分配属性](./media/block-connections-connectors/policy-assignment-basics.png)

1. 完成操作后，选择“查看 + 创建”。

   创建策略后，可能需要等待 15 分钟，然后策略才会生效。 变更可能也有类似的延迟影响。

1. 策略生效后，可以[测试策略](#test-policy)。

有关详细信息，请参阅[快速入门：创建策略分配以识别不合规资源](../governance/policy/assign-policy-portal.md)。

<a name="test-policy"></a>

## <a name="test-the-policy"></a>测试策略

若要试用策略，请在逻辑应用设计器中使用目前受限的连接器开始创建连接。 继续 Instagram 示例，当你登录到 Instagram 时，你会收到以下错误消息：你的逻辑应用无法创建连接：

![连接由于所应用的策略而失败](./media/block-connections-connectors/connection-failure-message.png)

该消息包含以下信息：

| 说明 | 内容 |
|-------------|---------|
| 失败原因 | `"Resource 'instagram' was disallowed by policy."` |
| 分配名称 | `"Block Instagram connections"` |
| 分配 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| 策略定义 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Policy](../governance/policy/overview.md)
