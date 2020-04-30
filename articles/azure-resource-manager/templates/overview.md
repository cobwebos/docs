---
title: 模板概述
description: 介绍使用 Azure 资源管理器模板部署资源的好处。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086328"
---
# <a name="what-are-arm-templates"></a>什么是 ARM 模板？

在迁移到云的过程中，许多团队都采用了敏捷开发方法。 这些团队的工作快速迭代。 他们需要反复将其解决方案部署到云，并需要知道其基础结构处于一种可靠的状态。 随着基础结构成为迭代过程的一部分，运营与开发之间的划分已经消失。 团队需要通过统一的过程来管理基础结构和应用程序代码。

为了解决这些难题，可将部署自动化，并运用基础结构即代码。 在代码中定义需要部署的基础结构。 基础结构代码将成为项目的一部分。 与应用程序代码一样，可将基础结构代码存储在源存储库中，并控制其版本。 团队中的任何人都可以运行该代码并部署类似的环境。

若要实现 Azure 解决方案的基础结构代码，请使用 Azure 资源管理器（ARM）模板。 该模板是一个定义项目基础结构和配置的 JavaScript 对象表示法 (JSON) 文件。 该模板使用声明性语法，使你可以声明要部署的内容，而不需要编写一系列编程命令来进行创建。 在该模板中，指定要部署的资源以及这些资源的属性。

## <a name="why-choose-arm-templates"></a>为什么选择 ARM 模板？

如果要尝试决定使用 ARM 模板和其他某个基础结构作为代码服务，请考虑使用模板的以下优点：

* **声明性语法**： ARM 模板允许以声明方式创建和部署整个 Azure 基础结构。 例如，不仅可以部署虚拟机，还可以部署网络基础结构、存储系统和可能需要的任何其他资源。

* 可**重复的结果**：在整个开发生命周期内重复部署你的基础结构，并确保以一致的方式部署你的资源。 模板是幂等的，这意味着，可以多次部署同一模板，并获得处于相同状态的相同资源类型。 可以开发一个模板来表示所需的状态，而无需开发大量的独立模板来表示更新。

* **业务流程**：不必担心排序操作的复杂性。 资源管理器会协调相互依赖的资源的部署，以按正确的顺序创建这些资源。 在可能的情况下，资源管理器将会并行部署资源，因此，其完成速度比串行部署更快。 通过一个命令部署模板，而无需使用多个强制性命令。

   ![模板部署的比较](./media/overview/template-processing.png)

* **模块化文件**：可以将模板分解为更小的可重复使用的组件，并在部署时将它们链接在一起。 还可以在一个模板中嵌套另一个模板。

* **创建任何 azure 资源**：可以立即在模板中使用新的 azure 服务和功能。 一旦资源提供程序引入了新资源，你就可以通过模板立即部署这些资源。 在使用新服务之前，无需等待工具或模块完成更新。

* **扩展性**：通过[部署脚本](deployment-script-template.md)，你可以将 PowerShell 或 Bash 脚本添加到模板。 部署脚本扩展了在部署过程中设置资源的能力。 脚本可以包含在模板中，也可以存储在外部源中并在模板中引用。 部署脚本使你能够在单个 ARM 模板中完成端到端环境的安装。

* **测试**：通过使用 arm 模板工具包（ttk）对模板进行测试，可以确保模板遵循建议的指导原则。 此测试工具包是一个 PowerShell 脚本，可从[GitHub](https://github.com/Azure/arm-ttk)下载。 使用该工具包，你可以更轻松地使用模板语言开发专业技能。

* **预览更改**：在部署模板之前，可以使用 "[假设" 操作](template-deploy-what-if.md)获取更改的预览。 对于-if，你会看到将创建、更新或删除的资源以及任何将更改的资源属性。 假设操作检查环境的当前状态，无需管理状态。

* **内置验证**：只有通过验证后才会部署模板。 资源管理器在开始部署之前会检查模板，以确保部署成功。 部署不太可能会在半完成状态时停止。

* **跟踪的部署**：在 Azure 门户中，你可以查看部署历史记录并获取有关模板部署的信息。 可以查看已部署的模板、已传入的参数值，以及任何输出值。 其他基础结构即代码服务不是通过门户跟踪的。

   ![部署历史记录](./media/overview/deployment-history.png)

* **策略即代码**： [Azure 策略](../../governance/policy/overview.md)是一种作为代码框架的策略，用于自动化管理。 如果使用 Azure 策略，在通过模板进行部署时，将会针对不合规的资源执行策略修正。

* **部署蓝图**：你可以利用 Microsoft 提供的[蓝图](../../governance/blueprints/overview.md)来满足法规和合规性标准。 这些蓝图包括用于各种体系结构的预建模板。

* **CI/CD 集成**：可以将模板集成到持续集成和持续部署（CI/CD）工具中，这可以自动执行发布管道，实现快速可靠的应用程序和基础结构更新。 通过使用 Azure DevOps 和资源管理器模板任务，你可以使用 Azure Pipelines 持续生成和部署 ARM 模板项目。 若要了解详细信息，请参阅[VS project with 管道](add-template-to-azure-pipelines.md)和[教程：将 Azure 资源管理器模板与 Azure Pipelines 持续集成](./deployment-tutorial-pipeline.md)。

* 可**导出代码**：可以通过导出资源组的当前状态或查看用于特定部署的模板，获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

* **创作工具**：可以[Visual Studio Code](use-vs-code-to-create-template.md)和模板工具扩展创建模板。 你将获得 Intellisense、语法突出显示、内联帮助以及其他许多语言功能。 除了 Visual Studio code 以外，还可以使用 [Visual Studio](create-visual-studio-deployment-project.md)。

## <a name="template-file"></a>模板文件

在模板中，可以编写[模板表达式](template-expressions.md)来扩展 JSON 的功能。 这些表达式使用资源管理器提供的[函数](template-functions.md)。

模板包含以下节：

* [参数](template-parameters.md) - 在部署过程中提供值，以便可将同一模板用于不同的环境。

* [变量](template-variables.md) - 定义在模板中重复使用的值。 可以从参数值构造变量。

* [用户定义的函数](template-user-defined-functions.md) - 创建自定义函数用于简化模板。

* [资源](template-syntax.md#resources) - 指定要部署的资源。

* [输出](template-outputs.md) - 从已部署的资源返回值。

## <a name="template-deployment-process"></a>模板部署过程

部署模板时，资源管理器会将模板转换为 REST API 操作。 例如，当资源管理器收到具有以下资源定义的模板：

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
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
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>模板设计

模板和资源组的定义方式完全取决于用户及其所需的解决方案管理方式。 例如，可以通过单个模板将三层应用程序部署到单个资源组。

![三层模板](./media/overview/3-tier-template.png)

但无需在单个模板中定义整个基础结构。 通常，合理的做法是将部署要求划分成一组有针对性的模板。 可以轻松地将这些模板重复用于不同的解决方案。 若要部署特定的解决方案，请创建链接所有所需模板的主模板。 下图显示了如何通过包含三个嵌套模板的父模板部署三层解决方案。

![嵌套层模板](./media/overview/nested-tiers-template.png)

要各层具有单独的生命周期，可将三个层部署到单独的资源组。 请注意，仍可将这些资源链接到其他资源组中的资源。

![层模板](./media/overview/tier-templates.png)

有关嵌套模板的信息，请参阅[将链接的模板用于 Azure 资源管理器](linked-templates.md)。

## <a name="next-steps"></a>后续步骤

* 有关指导你完成创建模板的过程的分步教程，请参阅[教程：创建和部署第一个 ARM 模板](template-tutorial-create-first-template.md)。
* 有关模板文件中的属性的信息，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 若要了解有关导出模板的信息，请参阅[快速入门：使用 Azure 门户创建和部署 ARM 模板](quickstart-create-templates-use-the-portal.md)。
