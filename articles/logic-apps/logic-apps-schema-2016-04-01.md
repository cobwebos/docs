---
title: 架构更新（2016 年 6 月 1 日）- Azure 逻辑应用 | Microsoft 文档
description: 针对 Azure 逻辑应用中的逻辑应用定义更新了架构版本 2016-06-01
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849766"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure 逻辑应用的架构更新 - 2016 年 6 月 1 日

适用于 Azure 逻辑应用的[更新架构](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)和 API 版本包含多项关键改进，使逻辑应用更可靠、更易于使用：

* [作用域](#scopes)允许用户以操作集合方式将操作组合或嵌套在一起。
* [条件和循环](#conditions-loops)现在是一流操作。
* 针对 `runAfter` 属性运行操作时排序更精确，替换了 `dependsOn`

要将逻辑应用从 2015 年 8 月 1 日的预览版架构升级到 2016 年 6 月 1 日的架构，请[查看升级部分](#upgrade-your-schema)。

<a name="scopes"></a>

## <a name="scopes"></a>作用域

此架构包含作用域，方便用户将操作组合或嵌套到一起。 例如，一个条件可以包含另一个条件。 请详细了解[作用域语法](../logic-apps/logic-apps-loops-and-scopes.md)，或者查看下面这个基本的作用域示例：

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>条件和循环更改

在以前的架构版本中，条件和循环是与单个操作关联的参数。 此架构提升了该限制，因此条件和循环现在都可以操作类型的形式提供。 详细了解[循环和作用域](../logic-apps/logic-apps-loops-and-scopes.md)、[条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，或者查看这个显示条件操作的基本示例：

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>“runAfter”属性

`runAfter` 属性替换了 `dependsOn`，根据以前操作的状态为操作指定运行顺序时更精确。 `dependsOn` 属性指示“操作是否已成功运行”，具体取决于上一个操作是成功、失败还是跳过 - 而不是指示你希望运行操作的次数。 `runAfter` 属性以对象方式提供灵活性，该对象指定对象在其后运行的所有操作名称。 该属性也定义可充当触发器的状态的数组。 例如，如果希望操作在操作 A 成功后运行，并且在操作 B 成功或失败后运行，请设置此 `runAfter` 属性：

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>升级架构

若要升级到[最新架构](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)，仅需要执行几个步骤。 升级过程包括运行升级脚本、另存为新的逻辑应用，以及在可能情况下根据需要覆盖旧逻辑应用。

1. 在 Azure 门户中打开逻辑应用。

2. 转到“概览”。 在逻辑应用工具栏上，选择“更新架构”。
   
   ![选择“更新架构”][1]
   
   此时会返回升级的定义，用户可以根据需要将其复制并粘贴到资源定义中。 

   > [!IMPORTANT]
   > *确保*选择“另存为”，以便所有连接引用在升级后的逻辑应用中保持有效。

3. 在升级边栏选项卡工具栏中，选择“另存为”。

4. 输入逻辑名称和状态。 若要部署升级的逻辑应用，请选择“创建”。

5. 确认已升级的逻辑应用正常运行。
   
   > [!NOTE]
   > 如果使用 manual 或 request 触发器，则回调 URL 会在新逻辑应用中更改。 请测试新的 URL，确保端到端体验正常。 若要保留以前的 URL，可以通过现有的逻辑应用进行克隆。

6. *可选* 若要使用新的架构版本覆盖以前的逻辑应用，请在工具栏上选择“更新架构”旁边的“克隆”。 仅当需要保留逻辑应用的同一资源 ID 或请求触发器 URL 时，此步骤才是必需的。

## <a name="upgrade-tool-notes"></a>升级工具说明

### <a name="mapping-conditions"></a>映射条件

在升级后的定义中，该工具尽量将 true 和 false 分支操作作为作用域组合在一起。 具体而言，`@equals(actions('a').status, 'Skipped')` 的设计器模式将显示为 `else` 操作。 但是，如果检测到无法识别的模式，该工具可能会为 true 和 false 分支创建单独条件。 可以根据需要在升级后重新映射操作。

#### <a name="foreach-loop-with-condition"></a>带条件的“foreach”循环

在新架构中，可以使用筛选器操作来复制使用 **For each** 循环（每项一个条件）的模式。 但是，升级时会自动进行更改。 条件会变为在 **For each** 循环之前出现的筛选器操作，仅返回与条件匹配的项数组，并将该数组传递给 **For each** 操作。 有关示例，请参阅[循环和作用域](../logic-apps/logic-apps-loops-and-scopes.md)。

### <a name="resource-tags"></a>资源标记

升级之后会删除资源标记，因此必须针对升级的工作流重置这些标记。

## <a name="other-changes"></a>其他更改

### <a name="renamed-manual-trigger-to-request-trigger"></a>已将“manual”触发器重命名为“request”触发器

`manual` 触发器类型已弃用，并已通过类型 `http` 将其重命名为 `request`。 对于使用触发器构建的那类模式，此更改加强了一致性。

### <a name="new-filter-action"></a>新“筛选器”操作

新的 `filter` 类型用于将大型数组筛选为小型项集，可以接受一个数组和一个条件，针对每个项对条件进行评估，返回项符合条件的数组。

### <a name="restrictions-for-foreach-and-until-actions"></a>“foreach”和“until”操作的限制

`foreach` 和 `until` 循环限制为单个操作。

### <a name="new-trackedproperties-for-actions"></a>新的针对操作的“trackedProperties”

操作现在可以有一个名为 `trackedProperties` 的额外属性，与 `runAfter` 和 `type` 属性同级。 此对象指定要包含在 Azure 诊断遥测中的特定操作输入或输出，作为工作流的一部分发出。 例如：

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>后续步骤
* [为逻辑应用创建工作流定义](../logic-apps/logic-apps-author-definitions.md)
* [创建逻辑应用的部署模板](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
