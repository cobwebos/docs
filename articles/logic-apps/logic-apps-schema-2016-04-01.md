---
title: "新架构版本 2016-06-01 | Microsoft Docs"
description: "了解如何为最新版本的逻辑应用编写 JSON 定义"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: aaacb76fe845ca8892e4fe3979be5ea6ac5e902b


---
# <a name="new-schema-version-2016-06-01"></a>新架构版本 2016-06-01
逻辑应用的新架构和 API 版本具有大量改进，可提高逻辑应用的可靠性和易用性。 有 3 个重要差异：

1. 添加了范围，它们是包含操作集合的操作。
2. 条件和循环是一流操作
3. 通过 `runAfter` 属性（替换了 `dependsOn`）更详细地进行执行排序

有关将逻辑应用从 2015-08-01-预览架构升级到 2016-06-01 架构的信息，请[查看下面的升级部分](#upgrading-to-2016-06-01-schema)。

## <a name="1-scopes"></a>1.范围
此架构中的最大变化之一是添加了范围以及能够相互嵌套操作。  将一组操作分组在一起时，或是需要相互嵌套操作（例如一个条件可以包含其他条件）时，这会非常有用。  可以在[此处](../logic-apps/logic-apps-loops-and-scopes.md)找到有关范围语法的更多详细信息，不过可以在下面找到一个简单范围示例：

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2.条件和循环更改
在以前版本的架构中，条件和循环是与单个操作关联的参数。  此限制已在此架构中解除，现在条件和循环会显示为一种操作类型。  可以在[本文中](../logic-apps/logic-apps-loops-and-scopes.md)找到详细信息，下面演示了条件操作的简单示例：

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3.RunAfter 属性
新的 `runAfter` 属性替换了 `dependsOn`，以帮助更精确地进行运行排序。  `dependsOn` 是“操作已运行并成功”的同义词，但是在许多时候，当上一个操作成功、失败或已跳过时，需要执行相应操作。  通过 `runAfter` 可以实现这种灵活性。  它是一个对象，指定在它之前运行的所有操作名称，并定义可接受用于触发的状态的数组。  例如，如果要在步骤 A 成功并且步骤 B 成功或失败之后运行，则会构造以下 `runAfter` 属性：

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>升级到 2016-06-01 架构
升级到新的 2016-06-01 架构只需几个步骤。  可以在[本文中](../logic-apps/logic-apps-schema-2016-04-01.md)找到有关架构中的更改的详细信息。  升级过程包括运行升级脚本、保存为新逻辑应用以及可能会覆盖旧逻辑应用（如果需要）。

1. 打开当前逻辑应用。
2. 单击工具栏中的“更新架构”按钮
   
    ![][1]
   
    会返回升级后的定义。  可以将此复制并粘贴到资源定义中（如果需要），但是我们**强烈建议**使用“另存为”按钮以确保所有连接引用都在升级后的逻辑应用中有效。
3. 单击升级边栏选项卡的工具栏中的“另存为”按钮。
4. 填写名称和逻辑应用状态，然后单击“创建”以部署升级逻辑应用。
5. 验证升级后的逻辑应用是否按预期方式工作。
   
   > [!NOTE]
   > 如果使用手动或请求触发器，则回调 URL 会在新逻辑应用中更改。  使用新 URL 验证它是否以端到端方式正常工作，可以在现有逻辑应用上克隆以保留以前的 URL。
   > 
   > 
6. 可选 使用工具栏中的“克隆”按钮（与上图中的“更新架构”图标相邻）将以前的逻辑应用覆盖为新的架构版本。  仅当要保留逻辑应用的相同资源 ID 或请求触发器 URL 时，这才是必需的。

### <a name="upgrade-tool-notes"></a>升级工具说明
#### <a name="condition-mapping"></a>条件映射
该工具在升级后的定义中，会尽量将 true 和 false 分支操作分组在范围中。  具体而言，`@equals(actions('a').status, 'Skipped')` 设计器模式应显示为 `else` 操作。  但是如果该工具检测到它无法识别的模式，则可能会为 true 和 false 分支创建单独条件。  操作可以在升级后重新映射（如果需要）。

#### <a name="foreach-with-condition"></a>具有条件的 ForEach
在新架构中，可以使用筛选器操作重复每个项一个条件的以前 foreach 循环模式。  这应在升级时自动进行。  条件会变为 foreach 循环前的筛选器操作（用于仅返回与条件匹配的项数组），该数组会传递到 foreach 操作中。  可以在[本文中](../logic-apps/logic-apps-loops-and-scopes.md)查看此方面的示例

#### <a name="resource-tags"></a>资源标记
资源标记会在升级时删除，你需要为升级后的工作流再次设置它们。

## <a name="other-changes"></a>其他更改
### <a name="manual-trigger-renamed-to-request-trigger"></a>手动触发器重命名为请求触发器
类型 `manual` 已弃用，并重命名为具有 `http` 种类的 `request`。  这与触发器用于构建的模式类型更加一致。

### <a name="new-filter-action"></a>新“筛选器”操作
如果在使用大型数组并需要将它筛选为较小的项集合，则可以使用新“筛选器”类型。  它接受一个数组和一个条件，会为每个项评估条件并返回满足条件的项的数组。

### <a name="foreach-and-until-action-restrictions"></a>ForEach 和 until 操作限制
foreach 和 until 循环限制为单个操作。

### <a name="trackedproperties-on-actions"></a>操作中的 TrackedProperties
操作现在具有一个名为 `trackedProperties` 的附加属性（与 `runAfter` 和 `type` 同级）。  它是一个对象，指定要包含在作为工作流一部分发出的 Azure 诊断遥测中的特定操作输入或输出。  例如：

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
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
* [使用逻辑应用工作流定义](../logic-apps/logic-apps-author-definitions.md)
* [创建逻辑应用部署模板](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png



<!--HONumber=Jan17_HO3-->


