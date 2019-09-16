---
title: Azure 资源管理器模板
description: 介绍如何使用 Azure 资源管理器模板来部署资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 6d0d162f0f6f3024f6b4b63b8df1df9fd413afc8
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965298"
---
# <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

随着迁移到云，许多团队都采用了 agile 开发方法。 这些团队很快就会循环。 他们需要将他们的解决方案反复部署到云，并且知道其基础结构处于可靠状态。 随着基础结构成为迭代过程的一部分，操作和开发之间的划分已消失。 团队需要通过一个统一的过程来管理基础结构和应用程序代码。

为了解决这些难题，你可以自动执行部署，并使用基础结构作为代码的做法。 在代码中，可以定义需要部署的基础结构。 基础结构代码将成为项目的一部分。 与应用程序代码一样，你可以将基础结构代码存储在源存储库中并将其版本。 你的团队中的任何人都可以运行代码并部署类似的环境。

若要实现 Azure 解决方案的基础结构代码，请使用 Azure 资源管理器模板。 该模板是一个 JavaScript 对象表示法（JSON）文件，用于定义项目的基础结构和配置。 该模板使用声明性语法，这使你可以陈述打算部署的内容，而无需编写编程命令序列来创建它。 在模板中，指定要部署的资源以及这些资源的属性。

## <a name="why-choose-resource-manager-templates"></a>为什么选择资源管理器模板？

如果要尝试决定使用资源管理器模板和其他某个基础结构作为代码服务，请考虑使用模板的以下优点：

* **声明性语法**：资源管理器模板允许以声明方式创建和部署整个 Azure 基础结构。 例如，你不仅可以部署虚拟机，还可以部署网络基础结构、存储系统和可能需要的任何其他资源。

* 可**重复的结果**：在整个开发生命周期内重复部署基础结构，并以一致的方式部署资源。 模板是幂等的，这意味着你可以多次部署同一模板并获取相同状态的相同资源类型。 你可以开发一个表示所需状态的模板，而不是开发多个单独的模板来表示更新。

* **业务流程**：您不必担心排序操作的复杂性。 资源管理器协调相互依赖的资源的部署，以便按正确的顺序创建这些资源。 如果可能，资源管理器会以并行方式部署资源，以便部署比串行部署更快。 通过一个命令而不是通过多个命令命令来部署模板。

   ![模板部署比较](./media/template-deployment-overview/template-processing.png)

* **内置验证**：只有通过验证后，才会部署模板。 资源管理器在开始部署之前检查模板，以确保部署成功。 你的部署不太可能在半完成状态中停止。

* **模块化文件**：你可以将模板分解为更小的可重复使用的组件，并在部署时将它们链接在一起。 还可以在另一个模板内嵌套一个模板。

* **创建任何 Azure 资源**：你可以立即在模板中使用新的 Azure 服务和功能。 一旦资源提供程序引入了新资源，就可以通过模板部署这些资源。 使用新服务之前，无需等待工具或模块进行更新。

* **跟踪的部署**：在 Azure 门户中，可以查看部署历史记录并获取有关模板部署的信息。 您可以查看部署的模板、传入的参数值以及任何输出值。 其他基础结构，因为代码服务不是通过门户进行跟踪。

   ![部署历史记录](./media/template-deployment-overview/deployment-history.png)

* **作为代码的策略**：[Azure 策略](../governance/policy/overview.md)是一种作为代码框架的策略，用于自动进行管理。 如果你使用的是 Azure 策略，则在通过模板部署时，将在不符合的资源上执行策略更正。

* **部署蓝图**：你可以利用 Microsoft 提供的[蓝图](../governance/blueprints/overview.md)来满足法规和合规性标准。 这些蓝图包括用于各种体系结构的预建模板。

* 可**导出代码**：可以通过导出资源组的当前状态或查看用于特定部署的模板，获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

* **创作工具**：您可以[Visual Studio Code](resource-manager-tools-vs-code.md)和模板工具扩展创建模板。 你将获得 intellisense、语法突出显示、行内帮助以及许多其他语言功能。

## <a name="template-file"></a>模板文件

在模板中，可以编写扩展 JSON 功能的[模板表达式](template-expressions.md)。 这些表达式使用由资源管理器提供的[函数](resource-group-template-functions.md)。

模板包含以下各节：

* [参数](template-parameters.md)-在部署过程中提供一些值，这些值允许将同一模板用于不同的环境。

* [变量](template-variables.md)-定义在模板中重复使用的值。 它们可从参数值构造。

* [用户定义的函数](template-user-defined-functions.md)-创建简化模板的自定义函数。

* [资源](resource-group-authoring-templates.md#resources)-指定要部署的资源。

* [输出](template-outputs.md)-从已部署的资源返回值。

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
* 若要显式设置依赖关系，以便在另一个资源之前部署一个资源，请参阅[在 Azure 资源管理器模板中定义依赖项](resource-group-define-dependencies.md)。
* 可以将资源添加到模板，并根据需要进行部署。 有关详细信息，请参阅[资源管理器模板中的条件部署](conditional-resource-deployment.md)。
* 您可以指定一个变量、属性或资源的多个实例，而不是模板中多次重复的 JSON 块。 有关详细信息，请参阅[Azure 资源管理器模板中的资源、属性或可变迭代](resource-group-create-multiple.md)。
* 若要了解有关导出模板的[信息，请参阅快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-the-portal.md)。
