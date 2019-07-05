---
title: 在 Azure 托管应用程序中的视图定义的概述 |Microsoft Docs
description: 介绍创建 Azure 托管应用程序的视图定义的概念。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478740"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure 托管应用程序中的视图定义项目

视图定义是 Azure 托管应用程序中的可选项目。 它允许以自定义概述页并添加更多视图，如指标和自定义资源。

本文提供了视图定义项目的概述以及它的功能。

## <a name="view-definition-artifact"></a>视图定义项目

必须命名为视图定义项目**viewDefinition.json**并放在相同的级别**createUiDefinition.json**并**mainTemplate.json**中.zip创建托管应用程序定义的包。 若要了解如何创建.zip 包和发布托管应用程序定义，请参阅[发布 Azure 托管应用程序定义](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>视图定义架构

**ViewDefinition.json**文件具有只有一个顶级`views`属性，它是数组的视图。 每个视图在托管应用程序用户界面中所示为单独的菜单项在表中的内容。 每个视图具有`kind`设置视图的类型的属性。 它必须设置为以下值之一：[概述](#overview)，[指标](#metrics)， [CustomResources](#custom-resources)。 有关详细信息，请参阅当前[viewDefinition.json 的 JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

示例 JSON 的视图定义：

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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
    ]
}

```

## <a name="overview"></a>概述

`"kind": "Overview"`

提供在此视图时**viewDefinition.json**，它将替代在托管应用程序中的默认概述页。

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

|属性|需要|描述|
|---------|---------|---------|
|标头的值开始缓存响应|否|概述页的标头。|
|description|否|托管应用程序的说明。|
|命令|否|请参阅概述页的其他工具栏按钮的数组[命令](#commands)。|

## <a name="metrics"></a>度量值

`"kind": "Metrics"`

度量视图，可收集和聚合数据从托管应用程序资源中[Azure Monitor 指标](../azure-monitor/platform/data-platform-metrics.md)。

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

|属性|需要|描述|
|---------|---------|---------|
|displayName|否|该视图显示的标题。|
|版本|否|用于呈现视图的平台的版本。|
|图表|是|图表的指标页的数组。|

### <a name="chart"></a>图表

|属性|需要|描述|
|---------|---------|---------|
|displayName|是|显示的图表的标题。|
|chartType|否|要用于此图表的可视化效果。 默认情况下，它使用折线图。 支持的图表类型： `Bar, Line, Area, Scatter`。|
|metrics|是|要显示在此图表上的度量值的数组。 若要了解有关在 Azure 门户中受支持的指标的详细信息，请参阅[支持的 Azure Monitor 指标](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>指标

|属性|需要|描述|
|---------|---------|---------|
|name|是|指标的名称。|
|aggregationType|是|要使用此指标的聚合类型。 支持的聚合类型： `none, sum, min, max, avg, unique, percentile, count`|
|命名空间|否|确定正确的度量值提供程序时要使用的其他信息。|
|resourceTagFilter|否|资源标记数组 (将用分隔`or`word) 将显示针对哪些指标。 基于资源类型筛选器应用。|
|resourceType|是|资源类型将为其显示度量值。|

## <a name="custom-resources"></a>自定义资源

`"kind": "CustomResources"`

您可以定义此类型的多个视图。 每个视图都表示**唯一**从自定义提供程序的自定义资源类型中定义您**mainTemplate.json**。 有关自定义提供程序的简介，请参阅 [Azure 自定义提供程序预览版概述](custom-providers-overview.md)。

在此视图中可以执行 GET、 PUT、 删除和发布自定义资源类型的操作。 POST 操作可以是全局的自定义操作或在上下文中的自定义资源类型的自定义操作。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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

|属性|需要|描述|
|---------|---------|---------|
|displayName|是|该视图显示的标题。 标题应**唯一**为每个 CustomResources 视图中您**viewDefinition.json**。|
|版本|否|用于呈现视图的平台的版本。|
|resourceType|是|自定义资源类型。 必须是**唯一**的自定义提供程序的自定义资源类型。|
|createUIDefinition|否|创建 UI 定义架构创建自定义资源的命令。 创建 UI 定义的简介，请参阅[CreateUiDefinition 入门](create-uidefinition-overview.md)|
|命令|否|请参阅 CustomResources 视图的其他工具栏按钮的数组[命令](#commands)。|
|列|否|自定义资源的列的数组。 如果未定义`name`列将显示默认情况下。 列必须具有`"key"`和`"displayName"`。 对于键，提供要在视图中显示的属性的密钥。 如果嵌套的使用点作为分隔符，例如，`"key": "name"`或`"key": "properties.property1"`。 显示名称，提供要在视图中显示的属性的显示名称。 此外可以提供`"optional"`属性。 当设置为 true，列在视图中隐藏默认情况下。|

## <a name="commands"></a>命令

命令是显示在页上的其他工具栏按钮的数组。 每个命令表示从 Azure 自定义提供程序中定义的 POST 操作**mainTemplate.json**。 自定义提供程序的简介，请参阅[Azure 自定义提供程序概述](custom-providers-overview.md)。

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

|属性|需要|描述|
|---------|---------|---------|
|displayName|是|命令按钮的显示的名称。|
|path|是|自定义提供程序的操作名称。 必须在定义操作**mainTemplate.json**。|
|icon|否|命令按钮的图标。 在中定义的受支持的图标列表[JSON 架构](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。|
|createUIDefinition|否|创建命令的用户界面定义架构。 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。|

## <a name="next-steps"></a>后续步骤

- 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
- 自定义提供程序的简介，请参阅[Azure 自定义提供程序概述](custom-providers-overview.md)。
