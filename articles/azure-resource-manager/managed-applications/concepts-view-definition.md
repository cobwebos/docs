---
title: 视图定义的概述
description: 介绍为 Azure 托管应用程序创建视图定义的概念。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: bff846b4b64778d5e40ea7f08f88faf3dde81d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371603"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>查看 Azure 托管应用程序中的定义项目

视图定义是 Azure 托管应用程序中的可选项目。 使用它可以自定义概述页，并添加更多视图，例如“指标”和“自定义”资源。

本文概述视图定义项目及其功能。

## <a name="view-definition-artifact"></a>视图定义项目

视图定义项目必须命名为 **viewDefinition.json**，并放置在用于创建托管应用程序定义的 .zip 包中 **createUiDefinition.json** 和 **mainTemplate.json** 所在的同一级别。 若要了解如何创建 .zip 包和发布托管应用程序定义，请参阅[发布 Azure 托管应用程序定义](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>视图定义架构

**viewDefinition.json** 文件只有一个顶级 `views` 属性（视图数组）。 每个视图作为目录中的独立菜单项显示在托管应用程序用户界面中。 每个视图有一个用于设置视图类型的 `kind` 属性。 该属性必须设置为以下值之一：[Overview](#overview)、[Metrics](#metrics)、[CustomResources](#custom-resources)、[Associations](#associations)。 有关详细信息，请参阅最新的 [viewDefinition.json 的 JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

视图定义的示例 JSON：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>概述

`"kind": "Overview"`

在 **viewDefinition.json** 中提供此视图时，它会替代托管应用程序中的默认“概述”页。

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|属性|必须|说明|
|---------|---------|---------|
|标头的值开始缓存响应|否|概述页的标题。|
|description|否|托管应用程序的说明。|
|命令|否|概述页的其他工具栏按钮的数组，请参阅[命令](#commands)。|

![屏幕截图显示具有测试操作控件的托管应用程序的概述，以运行演示应用程序。](./media/view-definition/overview.png)

## <a name="metrics"></a>指标

`"kind": "Metrics"`

使用指标视图可以从 [Azure Monitor 指标](../../azure-monitor/platform/data-platform-metrics.md)中的托管应用程序资源收集和聚合数据。

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|属性|必须|说明|
|---------|---------|---------|
|displayName|否|视图的显示标题。|
|版本|否|用于呈现视图的平台版本。|
|charts|是|指标页的图表数组。|

### <a name="chart"></a>图表

|属性|必须|说明|
|---------|---------|---------|
|displayName|是|图表的显示标题。|
|chartType|否|用于此图表的可视化效果。 默认使用折线图。 支持的图表类型：`Bar, Line, Area, Scatter`。|
|指标|是|要在此图表上绘制的指标数组。 若要详细了解 Azure 门户中支持的指标，请参阅 [Azure Monitor 支持的指标](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>指标

|属性|必须|说明|
|---------|---------|---------|
|name|是|指标的名称。|
|aggregationType|是|用于此指标的聚合类型。 支持的聚合类型：`none, sum, min, max, avg, unique, percentile, count`|
|命名空间|否|确定正确的指标提供程序时要使用的其他信息。|
|resourceTagFilter|否|要显示其指标的资源标记数组（使用单词 `or` 分隔）。 在资源类型筛选器的顶部应用。|
|resourceType|是|要显示其指标的资源类型。|

![屏幕截图显示了一个名为 "监视" 的页，即 "我的托管应用程序的指标视图"。](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>自定义资源

`"kind": "CustomResources"`

可以定义此类型的多个视图。 每个视图表示 **mainTemplate.json** 中定义的自定义提供程序中的**唯一**自定义资源类型。 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序预览版概述](../custom-providers/overview.md)。

在此视图中，可对自定义资源类型执行 GET、PUT、DELETE 和 POST 操作。 POST 操作可以是全局自定义操作，或自定义资源类型上下文中的自定义操作。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|属性|必须|说明|
|---------|---------|---------|
|displayName|是|视图的显示标题。 标题对于 **viewDefinition.json** 中的每个 CustomResources 视图应该**唯一**。|
|版本|否|用于呈现视图的平台版本。|
|resourceType|是|自定义资源类型。 必须是自定义提供程序的**唯一**自定义资源类型。|
|icon|否|视图的图标。 示例图标列表在 [JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定义。|
|createUIDefinition|否|为“创建自定义资源”命令创建 UI 定义架构。 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)|
|命令|否|CustomResources 视图的其他工具栏按钮数组，请参阅[命令](#commands)。|
|列|否|自定义资源的列数组。 如果未定义，则默认会显示 `name` 列。 该列必须具有 `"key"` 和 `"displayName"`。 对于键，请提供要在视图中显示的属性的键。 如果嵌套键，请使用句点作为分隔符，例如 `"key": "name"` 或 `"key": "properties.property1"`。 对于显示名称，请提供要在视图中显示的属性的显示名称。 还可以提供 `"optional"` 属性。 设置为 true 时，该列默认会在视图中隐藏。|

![屏幕截图显示名为 "测试自定义资源类型" 和 "控件自定义上下文操作" 的资源页。](./media/view-definition/customresources.png)

## <a name="commands"></a>命令

命令是显示在页面上的附加工具栏按钮数组。 每个命令表示 **mainTemplate.json** 中定义的 Azure 自定义提供程序中的一个 POST 操作。 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序概述](../custom-providers/overview.md)。

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|属性|必须|说明|
|---------|---------|---------|
|displayName|是|命令按钮的显示名称。|
|path|是|自定义提供程序操作名称。 该操作必须在 **mainTemplate.json** 中定义。|
|icon|否|命令按钮的图标。 示例图标列表在 [JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定义。|
|createUIDefinition|否|为命令创建 UI 定义架构。 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。|

## <a name="associations"></a>关联

`"kind": "Associations"`

可以定义此类型的多个视图。 使用此视图可以通过 **mainTemplate.json** 中定义的自定义提供程序，将现有资源链接到托管应用程序。 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序预览版概述](../custom-providers/overview.md)。

在此视图中，可以基于 `targetResourceType` 扩展现有的 Azure 资源。 选择某个资源时，它会向**公共**自定义提供程序创建加入请求，这会对资源应用副作用。 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|属性|必须|说明|
|---------|---------|---------|
|displayName|是|视图的显示标题。 标题对于 **viewDefinition.json** 中的每个 Associations 视图应该**唯一**。|
|版本|否|用于呈现视图的平台版本。|
|targetResourceType|是|目标资源类型。 这是要为资源加入显示的资源类型。|
|createUIDefinition|否|为“创建关联资源”命令创建 UI 定义架构。 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>寻求帮助

如果你遇到了 Azure 托管应用程序方面的问题，请尝试在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 `azure-managedapps` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

- 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
- 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序概述](../custom-providers/overview.md)。
- 有关使用 Azure 自定义提供程序创建 Azure 托管应用程序的详细说明，请参阅 [教程：使用自定义提供程序操作和资源类型创建托管应用程序](tutorial-create-managed-app-with-custom-provider.md)
