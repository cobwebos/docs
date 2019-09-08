---
title: Azure 资源管理器模板
description: 介绍如何使用 Azure 资源管理器模板来部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802015"
---
# <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

随着迁移到云，许多团队都采用了 agile 开发方法。 这些团队很快就会循环。 他们需要将他们的解决方案反复部署到云，并且知道其基础结构处于可靠状态。 随着基础结构成为迭代过程的一部分，操作和开发之间的划分已消失。 团队需要通过一个统一的过程来管理基础结构和应用程序代码。

为了解决这些难题，你可以自动执行部署，并使用基础结构作为代码的做法。 在代码中，可以定义需要部署的基础结构。 基础结构代码将成为项目的一部分。 与应用程序代码一样，你可以将基础结构代码存储在源存储库中并将其版本。 你的团队中的任何人都可以运行代码并部署类似的环境。

若要实现 Azure 解决方案的基础结构代码，请使用 Azure 资源管理器模板。 该模板是一个 JavaScript 对象表示法（JSON）文件，用于定义项目的基础结构和配置。 该模板使用声明性语法，这使你可以陈述打算部署的内容，而无需编写编程命令序列来创建它。 在模板中，指定要部署的资源以及这些资源的属性。

## <a name="benefits-of-resource-manager-templates"></a>资源管理器模板的优点

资源管理器模板具有以下优点：

* 以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

* 在整个开发生命周期内重复部署解决方案，并确保资源在一致状态中部署。

* 通过声明性模板而非脚本来管理基础结构。

如果尝试决定使用资源管理器模板还是使用其他某个基础结构作为代码服务，请考虑以下优点模板：

* 新的 Azure 服务和功能在模板中立即可用。 一旦资源提供程序引入了新资源，就可以通过模板部署这些资源。 对于其他基础结构作为代码服务，需要等待第三方为新资源实现接口。

* 模板部署由模板的单个提交来处理，而不是通过多个命令性命令进行处理。 资源管理器协调相互依赖的资源的部署，以便按正确的顺序创建这些资源。 它分析模板，并根据资源之间的引用确定部署的正确顺序。

   ![模板部署比较](./media/template-deployment-overview/template-processing.png)

* 模板部署在 Azure 门户中进行跟踪。 您可以查看部署历史记录并获取有关模板部署的信息。 您可以查看部署的模板、传入的参数值以及任何输出值。 其他基础结构，因为代码服务不是通过门户进行跟踪。

   ![部署历史记录](./media/template-deployment-overview/deployment-history.png)

* 模板部署进行预航班验证。 资源管理器在开始部署之前检查模板，以确保部署成功。 你的部署不太可能在半完成状态中停止。

* 如果你使用的是[Azure 策略](../governance/policy/overview.md)，则在通过模板部署时，将在不符合的资源上执行策略更正。

* Microsoft 提供了部署[蓝图](../governance/blueprints/overview.md)来满足法规和合规性标准。 这些蓝图包括用于各种体系结构的预建模板。

## <a name="idempotent"></a>幂

幂等只是指您可以多次运行相同的操作并获得相同的结果。 部署资源管理器模板是幂等的。 可以多次部署同一模板，并获取相同状态的相同资源类型。 这一概念很重要，因为它意味着您是将模板重新部署到现有资源组还是将模板部署到新的资源组。

假设已在资源组中部署了三个资源，然后决定需要添加第四个资源。 可以将第四个资源添加到现有模板，而不是创建仅包含新资源的新模板。 将新模板部署到已有三个资源的资源组时，资源管理器确定要执行的操作。

如果资源已存在于资源组中，并且请求中未包含对属性的任何更新，则不执行任何操作。 如果资源存在但属性已更改，则会更新现有资源。 如果该资源不存在，将创建新资源。

在部署完成后，您一定相信资源始终处于预期状态。

## <a name="template-file"></a>模板文件

在模板中，可以编写扩展 JSON 功能的[模板表达式](template-expressions.md)。 这些表达式使用由资源管理器提供的[函数](resource-group-template-functions.md)。

模板包含以下各节：

* [参数](template-parameters.md)-在部署过程中提供一些值，这些值允许将同一模板用于不同的环境。

* [变量](template-variables.md)-定义在模板中重复使用的值。 它们可从参数值构造。

* [用户定义的函数](template-user-defined-functions.md)-创建简化模板的自定义函数。

* [资源](resource-group-authoring-templates.md#resources)-指定要部署的资源。

* [输出](template-outputs.md)-从已部署的资源返回值。

## <a name="template-features"></a>模板功能

资源管理器分析依赖关系，以确保按正确的顺序创建资源。 大多数依赖项都是隐式确定的。 但是，您可以显式设置依赖关系，以确保在一个资源之前部署另一个资源。 有关详细信息，请参阅[在 Azure 资源管理器模板中定义依赖关系](resource-group-define-dependencies.md)。

可以将资源添加到模板，并根据需要进行部署。 通常，会传入一个参数值，该值指示是否需要部署资源。 有关详细信息，请参阅[资源管理器模板中的条件部署](conditional-resource-deployment.md)。

您可以使用复制元素来指定变量、属性或资源的多个实例，而不是模板中多次重复的 JSON 块。 有关详细信息，请参阅[Azure 资源管理器模板中的资源、属性或可变迭代](resource-group-create-multiple.md)。

## <a name="export-templates"></a>导出模板

可以通过导出资源组的当前状态或查看用于特定部署的模板，获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

从门户创建解决方案时，该解决方案会自动包含部署模板。 无需从头开始创建模板，因为可以从解决方案的模板着手，并根据特定需求自定义该模板。 有关示例，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-the-portal.md)。

## <a name="template-deployment-process"></a>模板部署进程

部署模板时，资源管理器会将模板转换为 REST API 操作。 例如，当 Resource Manager 收到具有以下资源定义的模板：

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
* 有关资源管理器服务（包括其管理功能）的简介，请参阅[Azure 资源管理器概述](resource-group-overview.md)。

