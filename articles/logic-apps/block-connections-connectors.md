---
title: 阻止特定 API 连接器的连接
description: 限制在 Azure 逻辑应用中创建和使用 API 连接
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 06/19/2020
ms.openlocfilehash: 6563f3e263867387332940db58abff62e085cded
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187687"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>阻止 Azure 逻辑应用中的连接器创建的连接

如果你的组织不允许使用 Azure 逻辑应用中的连接器连接到受限或未批准的资源，则可以阻止在逻辑应用工作流中创建和使用这些连接的功能。 使用[Azure 策略](../governance/policy/overview.md)，你可以定义和强制执行[策略](../governance/policy/overview.md#policy-definition)，阻止为要阻止的连接器创建或使用连接。 例如，出于安全原因，你可能想要阻止与特定社交媒体平台或其他服务和系统的连接。

本主题说明如何使用 Azure 门户设置阻止特定连接的策略，但你可以通过其他方式（例如，通过 Azure REST API、Azure PowerShell、Azure CLI 和 Azure 资源管理器模板）创建策略定义。 有关详细信息，请参阅[教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 要阻止的连接器的引用 ID。 有关详细信息，请参阅[查找连接器引用 ID](#connector-reference-ID)。

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>查找连接器引用 ID

如果已经有了一个具有要阻止的连接的逻辑应用，请按照 Azure 门户的[步骤进行](#connector-ID-portal)操作。 否则，请执行以下步骤：

1. 请访问[逻辑应用连接器列表](https://docs.microsoft.com/connectors/connector-reference/connector-reference-logicapps-connectors)。

1. 查找要阻止的连接器的 "引用" 页。

   例如，如果想要阻止 Instagram 连接器，请参阅此页： 
   
   `https://docs.microsoft.com/connectors/instagram/`

1. 在该页的 URL 中，复制并保存末尾处的连接器引用 ID，而不是正斜杠 (`/`) ，例如， `instagram` 。

   稍后，在创建策略定义时，将在定义的条件语句中使用此 ID，例如：

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure 门户

1. 在[Azure 门户](https://portal.azure.com)中，查找并打开逻辑应用。

1. 在逻辑应用菜单上，选择 "**逻辑应用代码视图**"，以便查看逻辑应用的 JSON 定义。

   ![打开 "逻辑应用代码视图" 以查找连接器 ID](./media/block-connections-connectors/code-view-connector-id.png)

1. 查找 `parameters` 包含对象的对象，该对象 `$connections` 包括 `{connection-name}` 逻辑应用中每个连接的对象，并指定有关该连接的信息：

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

1. 在 `id` 属性值中，复制并保存端的连接器引用 ID，例如 `instagram` 。

   稍后，在创建策略定义时，将在定义的条件语句中使用此 ID，例如：

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>创建策略以阻止创建连接

若要阻止在逻辑应用中完全创建连接，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。 在门户的搜索框中，输入 `policy` ，然后选择 "**策略**"。

   ![在 Azure 门户中，查找并选择 "策略"](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 "**策略**" 菜单的 "**创作**" 下，选择 "**定义**  >  **+ 策略定义**"。

   ![选择 "定义" > "+ 策略定义"](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在 "**策略定义**" 下，根据示例中所述的属性，提供策略定义的信息：

   ![策略定义属性](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | 属性 | 必须 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **定义位置** | 是 | <*Azure-subscription-name*> | 用于策略定义的 Azure 订阅 <p><p>1. 若要查找你的订阅，请选择省略号 (**...**) "按钮。 <br>2. 从 "**订阅**" 列表中，找到并选择你的订阅。 <br>3. 完成后，选择 "**选择**"。 |
   | **名称** | 是 | <*策略定义-名称*> | 要用于策略定义的名称 |
   | **说明** | 不适合 | <*策略定义-名称*> | 策略定义的说明 |
   | **类别** | 适合 | **逻辑应用** | 策略定义的现有类别或新类别的名称 |
   | **策略强制** | 适合 | **已启用** | 此设置指定在保存工作时是否启用或禁用策略定义。 |
   ||||

1. 在 "**策略规则**" 下，将使用策略定义模板预先填充 JSON 编辑框。 根据下表中所述的属性将此模板替换为[策略定义](../governance/policy/concepts/definition-structure.md)，并遵循以下语法：

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

   | 属性 | “值” | 描述 |
   |----------|-------|-------------|
   | `mode` | `All` | 确定策略计算的资源类型的模式。 <p><p>此方案将设置 `mode` 为 `All` ，这会将策略应用到 Azure 资源组、订阅和所有资源类型。 <p><p>有关详细信息，请参阅[策略定义结构-模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 确定何时强制实施策略规则的条件 <p><p>在此方案中， `{condition-to-evaluate}` 确定中的 `api.id` 值是否 `Microsoft.Web/connections/api.id` 与匹配 `*managedApis/{connector-name}` ，后者指定了通配符 ( * ) 值。 <p><p>有关详细信息，请参阅[策略定义结构-策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `field` | `Microsoft.Web/connections/api.id` | `field`要与条件进行比较的值 <p><p>在此方案中， `field` 使用[*别名*](../governance/policy/concepts/definition-structure.md#aliases) `Microsoft.Web/connections/api.id` 访问连接器属性中的值 `api.id` 。 |
   | `like` | `*managedApis/{connector-name}` | 用于比较值的逻辑运算符和值 `field` <p><p>在此方案中， `like` 运算符和通配符 ( * ) 字符均可确保规则正常运行，而不考虑区域，字符串 `*managedApis/{connector-name}` 是要匹配的值，其中 `{connector-name}` 是要阻止的连接器的 ID。 <p><p>例如，假设要阻止创建到社交媒体平台或数据库的连接： <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 或 Azure SQL：`sql` <p><p>若要查找这些连接器 Id，请参阅本主题前面的[查找连接器引用 ID](#connector-reference-ID) 。 |
   | `then` | `{effect-to-apply}` | 满足条件时要应用的效果 `if` <p><p>在这种情况下，将 `{effect-to-apply}` 阻止和失败不符合策略的请求或操作。 <p><p>有关详细信息，请参阅[策略定义结构-策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect`用于阻止请求，即创建指定的连接 <p><p>有关详细信息，请参阅[了解 Azure 策略影响-拒绝](../governance/policy/concepts/effects.md#deny)。 |
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

   **策略规则**框的显示方式如下：

   ![策略定义的规则](./media/block-connections-connectors/policy-definition-create-connections-2.png)

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

1. 完成后，选择“保存”。 保存策略定义后，Azure 策略会生成更多的属性值并将其添加到策略定义。

1. 接下来，若要为策略定义强制实施策略，请[创建策略分配](#create-policy-assignment)。

有关 Azure 策略定义的详细信息，请参阅以下主题：

* [策略结构定义](../governance/policy/concepts/definition-structure.md)
* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
* [Azure 逻辑应用的 Azure Policy 内置策略定义](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>创建要阻止使用连接的策略

在逻辑应用中创建连接时，该连接作为单独的 Azure 资源存在。 如果仅删除逻辑应用，则连接不会自动删除，而是在被删除之前一直存在。 你可能会遇到这样的情况：连接已存在，或者必须在逻辑应用外部创建连接。 你仍可以通过创建阻止保存具有受限或未批准连接的逻辑应用的策略，阻止在逻辑应用中使用现有连接的功能。

1. 登录 [Azure 门户](https://portal.azure.com)。 在门户的搜索框中，输入 `policy` ，然后选择 "**策略**"。

   ![在 Azure 门户中，查找并选择 "策略"](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 "**策略**" 菜单的 "**创作**" 下，选择 "**定义**  >  **+ 策略定义**"。

   ![选择 "定义" > "+ 策略定义"](./media/block-connections-connectors/add-new-policy-definition.png)

1. 在 "**策略定义**" 下，根据示例中描述的属性提供策略定义的信息，并使用 Instagram 作为示例继续操作：

   ![策略定义属性](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **定义位置** | 是 | <*Azure-subscription-name*> | 用于策略定义的 Azure 订阅 <p><p>1. 若要查找你的订阅，请选择省略号 (**...**) "按钮。 <br>2. 从 "**订阅**" 列表中，找到并选择你的订阅。 <br>3. 完成后，选择 "**选择**"。 |
   | **名称** | 是 | <*策略定义-名称*> | 要用于策略定义的名称 |
   | **说明** | 不适合 | <*策略定义-名称*> | 策略定义的说明 |
   | **类别** | 适合 | **逻辑应用** | 策略定义的现有类别或新类别的名称 |
   | **策略强制** | 适合 | **已启用** | 此设置指定在保存工作时是否启用或禁用策略定义。 |
   ||||

1. 在 "**策略规则**" 下，将使用策略定义模板预先填充 JSON 编辑框。 根据下表中所述的属性将此模板替换为[策略定义](../governance/policy/concepts/definition-structure.md)，并遵循以下语法：

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

   | 属性 | “值” | 说明 |
   |----------|-------|-------------|
   | `mode` | `All` | 确定策略计算的资源类型的模式。 <p><p>此方案将设置 `mode` 为 `All` ，这会将策略应用到 Azure 资源组、订阅和所有资源类型。 <p><p>有关详细信息，请参阅[策略定义结构-模式](../governance/policy/concepts/definition-structure.md#mode)。 |
   | `if` | `{condition-to-evaluate}` | 确定何时强制实施策略规则的条件 <p><p>在此方案中， `{condition-to-evaluate}` 确定的字符串输出是否 `[string(field('Microsoft.Logic/workflows/parameters'))]` 包含字符串 `{connector-name}` 。 <p><p>有关详细信息，请参阅[策略定义结构-策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 要与条件进行比较的值 <p><p>在此方案中， `value` 是从的字符串输出 `[string(field('Microsoft.Logic/workflows/parameters'))]` ，它将 `$connectors` 对象内的对象转换 `Microsoft.Logic/workflows/parameters` 为字符串。 |
   | `contains` | `{connector-name}` | 要用于与属性进行比较的逻辑运算符和值 `value` <p><p>在此方案中， `contains` 运算符确保规则正常运行，而不考虑其中显示的位置 `{connector-name}` ，其中 string `{connector-name}` 是要限制或阻止的连接器的 ID。 <p><p>例如，假设要阻止使用连接到社交媒体平台或数据库： <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 或 Azure SQL：`sql` <p><p>若要查找这些连接器 Id，请参阅本主题前面的[查找连接器引用 ID](#connector-reference-ID) 。 |
   | `then` | `{effect-to-apply}` | 满足条件时要应用的效果 `if` <p><p>在这种情况下，将 `{effect-to-apply}` 阻止并使不符合策略的请求或操作失败。 <p><p>有关详细信息，请参阅[策略定义结构-策略规则](../governance/policy/concepts/definition-structure.md#policy-rule)。 |
   | `effect` | `deny` | `effect`是 `deny` 或阻止请求保存使用指定连接的逻辑应用 <p><p>有关详细信息，请参阅[了解 Azure 策略影响-拒绝](../governance/policy/concepts/effects.md#deny)。 |
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

   **策略规则**框的显示方式如下：

   ![策略定义的规则](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 完成后，选择“保存”。 保存策略定义后，Azure 策略会生成更多的属性值并将其添加到策略定义。

1. 接下来，若要为策略定义强制实施策略，请[创建策略分配](#create-policy-assignment)。

有关 Azure 策略定义的详细信息，请参阅以下主题：

* [策略结构定义](../governance/policy/concepts/definition-structure.md)
* [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
* [Azure 逻辑应用的 Azure Policy 内置策略定义](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>创建策略分配

接下来，需要分配要在其中强制执行策略的策略定义，例如，分配给单个资源组、多个资源组、Azure Active Directory (Azure AD) 租户或 Azure 订阅。 对于此任务，请按照以下步骤创建策略分配：

1. 如果已注销，请重新登录到[Azure 门户](https://portal.azure.com)。 在门户的搜索框中，输入 `policy` ，然后选择 "**策略**"。

   ![在 Azure 门户中，查找并选择 "策略"](./media/block-connections-connectors/find-select-azure-policy.png)

1. 在 "**策略**" 菜单的 "**创作**" 下，选择 "**分配**  >  **分配策略**"。

   ![选择 "分配" > "分配"](./media/block-connections-connectors/add-new-policy-assignment.png)

1. 在 "**基本**信息" 下，为策略分配提供以下信息：

   | 属性 | 必须 | 描述 |
   |----------|----------|-------------|
   | **范围** | 适合 | 要在其中实施策略分配的资源。 <p><p>1. 在 "**作用域**" 框的旁边，选择省略号 (**...**) "按钮。 <br>2. 从 "**订阅**" 列表中，选择 "Azure 订阅"。 <br>3. 根据需要，从**资源组**列表中选择资源组。 <br>4. 完成后，选择 "**选择**"。 |
   | **排除项** | 不适合 | 要从策略分配中排除的任何 Azure 资源。 <p><p>1. 在 "**排除**" 框的旁边，选择省略号 (**...**) "按钮。 <br>2. 从 "**资源**" 列表中，选择 >**添加到所选作用域**的资源。 <br>3. 完成后，选择 "**保存**"。 |
   | **策略定义** | 适合 | 要分配和强制实施的策略定义的名称。 此示例将继续执行示例 Instagram 策略 "阻止 Instagram 连接"。 <p><p>1. 在 "**策略定义**" 框的旁边，选择省略号 (**...**) "按钮。 <br>2. 使用 "**类型**筛选器" 或 "**搜索**" 框查找并选择策略定义。 <br>3. 完成后，选择 "**选择**"。 |
   | **分配名称** | 适合 | 用于策略分配的名称（如果不同于策略定义） |
   | **分配 ID** | 适合 | 为策略分配自动生成的 ID |
   | **说明** | 不适合 | 策略分配的说明 |
   | **策略强制** | 适合 | 启用或禁用策略分配的设置 |
   | **分配者** | 不适合 | 创建和应用策略分配的人员的姓名 |
   ||||

   例如，使用 Instagram 示例将策略分配给 Azure 资源组：

   ![策略分配属性](./media/block-connections-connectors/policy-assignment-basics.png)

1. 完成操作后，选择“查看 + 创建”。

   创建策略后，你可能需要等待15分钟，然后策略才会生效。 更改可能也具有类似的延迟影响。

1. 策略生效后，可以[测试策略](#test-policy)。

有关详细信息，请参阅[快速入门：创建策略分配以识别不合规的资源](../governance/policy/assign-policy-portal.md)。

<a name="test-policy"></a>

## <a name="test-the-policy"></a>测试策略

若要尝试策略，请在逻辑应用设计器中使用 "当前限制的连接器" 开始创建连接。 继续 Instagram 示例，当你登录到 Instagram 时，你会收到以下错误消息：你的逻辑应用无法创建连接：

![由于应用策略而导致连接失败](./media/block-connections-connectors/connection-failure-message.png)

该消息包含以下信息：

| 说明 | 内容 |
|---|---|
| 失败原因 | `"Resource 'instagram' was disallowed by policy."` |
| 分配名称 | `"Block Instagram connections"` |
| 分配 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| 策略定义 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 策略](../governance/policy/overview.md)的详细信息