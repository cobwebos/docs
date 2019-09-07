---
title: Azure 资源管理器模板
description: 介绍如何使用 Azure 资源管理器模板来部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390761"
---
# <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

随着迁移到云，许多团队都采用了 agile 开发方法。 这些团队很快就会循环。 他们需要重复并可靠地将其解决方案部署到云中。 由于团队需要在单个进程中管理基础结构和源代码，操作和开发之间的划分已消失。

解决这些难题的一种方法是自动进行部署，并使用基础结构作为代码的做法。 您可以使用代码来定义需要部署的内容，并通过与源代码相同的过程来管理该代码。 将基础结构存储为源存储库中的代码并对其进行版本。

Azure 资源管理器使你能够通过资源管理器模板将基础结构作为代码实现。 该模板是一个 JavaScript 对象表示法（JSON）文件，其中包含 Azure 解决方案的基础结构和配置。 该模板使用声明性语法，这使你可以陈述打算部署的内容，而无需编写编程命令序列来创建它。 在模板中，指定要部署的资源以及这些资源的属性。

## <a name="benefits-of-resource-manager-templates"></a>资源管理器模板的优点

资源管理器模板具有几个优点。 你可以：

* 以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

* 在整个开发生命周期内重复部署解决方案，并确保资源在一致状态中部署。

* 通过声明性模板而非脚本来管理基础结构。

* 定义资源之间的依赖关系，以便按正确的顺序部署它们。

* 立即利用新的版本和服务，因为其他方不需要执行任何中间工作。

## <a name="template-file"></a>模板文件

在模板中，可以编写扩展 JSON 功能的[模板表达式](template-expressions.md)。 这些表达式使用由资源管理器提供的[函数](resource-group-template-functions.md)。

模板包含以下各节：

* [参数](template-parameters.md)-在部署过程中提供一些值，这些值允许将同一模板用于不同的环境。

* [变量](template-variables.md)-定义在模板中使用的值。

* [用户定义的函数](template-user-defined-functions.md)-创建简化模板的自定义函数。

* [资源](resource-group-authoring-templates.md#resources)-指定要部署的资源。

* [输出](template-outputs.md)-从已部署的资源返回值。

## <a name="dependencies"></a>依赖项

Azure 资源管理器会分析依赖关系，以确保按正确的顺序创建资源。 如果一个资源依赖于另一个资源（例如虚拟机需要存储帐户才能访问磁盘）中的值，请设置依赖关系。 有关详细信息，请参阅[在 Azure 资源管理器模板中定义依赖关系](resource-group-define-dependencies.md)。

## <a name="conditional-deployment"></a>条件部署

可以将资源添加到模板，并根据需要进行部署。 通常，会传入一个参数值，该值指示是否需要部署资源。 有关详细信息，请参阅[资源管理器模板中的条件部署](conditional-resource-deployment.md)。

## <a name="create-multiple-instances"></a>创建多个实例

您可以使用复制元素来指定变量、属性或资源的多个实例，而不是模板中多次重复的 JSON 块。 有关详细信息，请参阅[Azure 资源管理器模板中的资源、属性或可变迭代](resource-group-create-multiple.md)。

## <a name="export-templates"></a>导出模板

可以通过导出资源组的当前状态或查看用于特定部署的模板，获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

从门户创建解决方案时，该解决方案会自动包含部署模板。 无需从头开始创建模板，因为可以从解决方案的模板着手，并根据特定需求自定义该模板。 有关示例，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-the-portal.md)。

## <a name="template-deployment-process"></a>模板部署进程

部署模板时，资源管理器分析模板并将其语法转换为 REST API 操作。 例如，当 Resource Manager 收到具有以下资源定义的模板：

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

它将定义转换为以下 REST API 操作，后者将发送到 Microsoft.Storage 资源提供程序：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

如果资源已存在于资源组中，并且请求中未包含对属性的任何更新，则不执行任何操作。 如果资源存在但属性已更改，则会更新现有资源。 如果该资源不存在，将创建新资源。 利用此方法，你可以安全地重新部署模板并了解资源保持一致的状态。

## <a name="template-design"></a>模板设计

模板和资源组的定义方式完全取决于用户及其所需的解决方案管理方式。 例如，可以通过单个模板在单个资源组中部署三层式应用程序。

![三层模板](./media/template-deployment-overview/3-tier-template.png)

但无需在单个模板中定义整个基础结构。 通常，合理的做法是将部署要求划分成一组有针对性的模板。 可以轻松地将这些模板重复用于不同的解决方案。 若要部署特定的解决方案，请创建链接所有所需模板的主模板。 下图显示了如何通过包含三个嵌套模板的父模板部署三层解决方案。

![嵌套层模板](./media/template-deployment-overview/nested-tiers-template.png)

要各层具有单独的生命周期，可将三个层部署到单独的资源组。 请注意，资源仍可链接到其他资源组中的资源。

![层模板](./media/template-deployment-overview/tier-templates.png)

有关嵌套模板的信息，请参阅[将链接的模板用于 Azure 资源管理器](resource-group-linked-templates.md)。

## <a name="next-steps"></a>后续步骤

* 有关模板文件中的属性的信息，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
* 若要开始开发模板，请参阅[使用 Visual Studio Code 创建 Azure 资源管理器模板](resource-manager-tools-vs-code.md)。


