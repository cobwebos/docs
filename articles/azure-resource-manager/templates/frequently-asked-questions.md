---
title: ARM 模板常见问题解答
description: 有关 Azure 资源管理器模板的常见问题解答 (FAQ)。
ms.topic: conceptual
ms.date: 05/30/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: fb4bac00c4d971e7b056c18fe609d77b077dbcbb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85296859"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>有关 ARM 模板的常见问题解答

本文解答有关 Azure 资源管理器 (ARM) 模板的常见问题。

## <a name="getting-started"></a>入门

* **什么是 ARM 模板，为什么要使用它们？**

  ARM 模板是 JSON 文件，可在其中定义要部署到 Azure 的内容。 模板可帮助你实现 Azure 的基础结构即代码解决方案。 组织可以重复并可靠地将所需基础结构部署到不同的环境。
  
  若要详细了解 ARM 模板如何帮助管理 Azure 基础结构，请参阅[什么是 ARM 模板？](overview.md)

* **如何开始使用模板？**

  若要简化 ARM 模板的创作，需要合适的工具。 我们建议安装 [Visual Studio Code](https://code.visualstudio.com/) 和 [Azure 资源管理器工具扩展](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。 有关这些工具的简要介绍，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](quickstart-create-templates-use-visual-studio-code.md)。

  准备好学习如何创建 ARM 模板时，请开始学习[有关 ARM 模板的初学者教程系列](template-tutorial-create-first-template.md)。 这些教程指导你逐步完成构造 ARM 模板的过程。 你将了解模板的不同部分以及它们的协作方式。 此内容也作为[Microsoft Learn 模块](/learn/modules/authoring-arm-templates/)提供。

* **我应使用 ARM 模板还是 Terraform 部署到 Azure？**

  可使用你最喜欢的选项。 这两项服务都可帮助你自动部署到 Azure。
  
  我们认为，使用 ARM 模板比使用其他基础结构即代码服务更加有益。 若要了解这些优势，请参阅[为什么选择 ARM 模板？](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Build 2020

* **我错过了 Microsoft Build 2020 的演示文稿。演示文稿是否可供查看？**

  是的，请[随时观看](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions)。

* **在哪里可以获得有关在 Build 发布的新功能的详细信息？**

  有关正在使用的功能的一般信息，请加入我们的 [Azure 顾问部署 Yammer 组](https://aka.ms/ARMMeet)。

  若要了解有关新模板语言的信息，请[注册通知](https://aka.ms/armLangUpdates)。

  对于模板规格的预览，请[加入等待列表](https://aka.ms/templateSpecsWaitlist)。

## <a name="creating-and-testing-templates"></a>创建和测试模板

* **在哪里可以了解 ARM 模板的最佳做法？**

  有关实现模板的建议，请参阅 [ARM 模板的最佳做法](template-best-practices.md)。 创建模板后，运行 [ARM 测试工具包](https://github.com/azure/arm-ttk)。 它检查模板是否符合建议的做法。

* **我已通过门户设置我的环境。是否有某种方法可从现有资源组获取模板？**

  是的，你可以从资源组[导出模板](export-template-portal.md)。 导出的模板是了解模板的良好起点，但你可能想要在将其用于生产环境之前对其进行修订。
  
  导出模板时，可以选择要包含在模板中的资源。

* **是否可以在 ARM 模板中创建资源组并向其部署资源？**

  是的，当你在 Azure 订阅级别部署模板时，可以在模板中创建资源组。 有关创建资源组和部署资源的示例，请参阅[资源组和资源](deploy-to-subscription.md#resource-group-and-resources)。

* **是否可以在 ARM 模板中创建订阅？**

  不可以，但我们正努力开发此功能。

* **如何在部署模板之前对其进行测试？**

  建议在部署模板之前，在其上运行 [ARM 测试工具包](https://github.com/azure/arm-ttk)和 [what-if 操作](template-deploy-what-if.md)。 测试工具包将检查模板是否使用最佳做法。 它在识别到可改进模板实现方式的更改时提供警告。

  What-if 操作显示模板对环境所做的更改。 在部署之前，可以看到意外的更改。 What-if 操作还会返回预检验证期间可以检测到的任何错误。 例如，如果模板中包含语法错误，则会返回该错误。 它还会返回任何可以确定已部署资源的最终状态的错误。 例如，如果模板使用已在使用中的名称部署存储帐户，What-if 操作会返回该错误。

* **在哪里可以找到可用于每种资源类型的属性的相关信息？**

  VS Code 提供了用于处理资源属性的 Intellisense。 还可以查看属性和说明的[模板参考](/azure/templates/)。

* **我需要创建一个资源类型的多个实例。如何在我的模板中创建迭代器？**

  使用 copy 元素指定多个实例。 还可以将 copy 用于[资源](copy-resources.md)、[属性](copy-properties.md)、[变量](copy-variables.md)和[输出](copy-outputs.md)。

## <a name="template-language"></a>模板语言

* **我听说你正在研究一种新的模板语言。在哪里可以找到有关它的详细信息？**

  若要了解有关新模板语言的信息，请[注册通知](https://aka.ms/armLangUpdates)。

* **是否有支持在 YAML 中创建模板的计划？**

  目前没有支持 YAML 的计划。 我们相信，新模板语言将提供比 YAML 或 JSON 更易于使用的解决方案。

* **发布新模板语言后，是否仍可以使用 JSON 编写模板？**

  是的，你可以继续使用 JSON 模板。

* **是否会提供将我的 JSON 模板转换为新模板语言的工具？**

  是的。

## <a name="template-specs"></a>模板规格

* **如何加入模板规格的预览版本？**

  [加入模板规格的等待列表](https://aka.ms/templateSpecsWaitlist)。

* **模板规格和 Azure 蓝图有何关系？**

  Azure 蓝图通过将 `blueprint definition` 资源替换为 `template spec` 资源在其实现中使用模板规格。 我们将提供迁移路径，以将蓝图定义转换为模板规格，但仍支持蓝图定义 API。 没有对 `blueprint assignment` 资源进行更改。 蓝图仍是在 Azure 中构建受治理环境的用户体验。

* **模板规格是否会替换链接模板？**

  不会，但模板规格设计为可与链接模板一起使用。 在部署父模板之前，无需将链接模板移动到可公开访问的终结点。 而是在创建模板规格时，将父模板及其项目打包在一起。

* **是否可以跨订阅共享模板规格？**

  可以，只要用户具有对模板规格的读取访问权限，就可以跨订阅使用它们。不能跨租户使用模板规格。

## <a name="scripts-in-templates"></a>模板中的脚本

* **是否可以在模板中包含脚本来执行模板中无法执行的任务？**

  可以，请使用[部署脚本](deployment-script-template.md)。 可在模板中包含 Azure PowerShell 或 Azure CLI 脚本。 此功能以预览版提供。

* **是否仍可使用自定义脚本扩展和 Desired State Configuration (DSC)？**

  这些选项仍可用且未发生更改。 部署脚本旨在执行与 VM 来宾无关的操作。 如果需要在 VM 中的主机操作系统上运行脚本，则自定义脚本扩展和/或 DSC 将是更好的选择。 但部署脚本具有一些优势，例如可设置超时持续时间。

* **Azure 政府版是否支持部署脚本？**

  支持，你可以使用 US Gov 亚利桑那州和 US Gov 弗吉尼亚州中的部署脚本。

## <a name="preview-changes-before-deployment"></a>在部署前预览更改

* **是否可以预览部署模板之前将发生的更改？**

  可以，使用 [what-if 功能](template-deploy-what-if.md)。 它会评估环境的当前状态，并将其与部署后存在的状态进行比较。 你可以检查汇总的更改，以确保模板没有任何意外的结果。

* **可以同时在增量模式和完全模式下使用 what-if 功能吗？**

  可以，两种[部署模式](deployment-modes.md)均受支持。 有关使用增量模式的示例，请参阅[运行 what-if 操作](template-deploy-what-if.md#run-what-if-operation)。 有关使用完全模式的示例，请参阅[确认删除](template-deploy-what-if.md#confirm-deletion)。

* **What-if 是否适用于链接模板？**

  适用，what-if 可评估父模板及其链接模板的状态。

* **是否可以在 Azure 管道中使用 what-if？**

  可以，你可以使用 what-if 来验证管道是否应继续。

* **使用 what-if 时，我看到了不属于模板的属性更改。这是预期的“干扰信息”吗？**

  What-if 功能处于预览阶段。 我们正在努力减少干扰信息。 请在此处的 GitHub 存储库中提交问题，以帮助我们改进： https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>模板可视化工具

* **是否有办法可视化 ARM 模板及其资源？**

  我们有一个[社区提供的 VS Code 扩展](https://aka.ms/ARMVisualizer)，它可以很好地可视化 ARM 模板。 该扩展显示了要部署的资源以及它们之间的关系。

* **是否可以在 VS Code 之外使用模板可视化工具？**

  可在门户中预览模板可视化工具。 有关详细信息，请观看此 [Build 的简短会话](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions)。

## <a name="deployment-limits"></a>部署限制

* **在单个部署操作中可以部署多少个资源组？**

  过去，此限额为五个资源组。 最近已增加到 800 个资源组。 有关详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* **在部署历史记录中，我遇到关于限额为 800 个部署的错误。**

  我们正在更改资源组部署历史记录的维护方式。 过去，必须手动从此历史记录中删除部署才能避免此错误。 从 2020 年 6 月开始，我们将在你接近限额时自动从历史记录中删除部署。 有关详细信息，请参阅[从部署历史记录中自动删除](deployment-history-deletions.md)。

  从历史记录中删除部署不会影响已部署的资源。

## <a name="templates-and-devops"></a>模板和 DevOps

* **是否可以将 ARM 模板集成到 Azure Pipelines 中？**

  是的。 有关使用模板和管道的说明，请参阅[教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](deployment-tutorial-pipeline.md)和[将 ARM 模板与 Azure Pipelines 集成](add-template-to-azure-pipelines.md)。

* **是否可以使用 GitHub 操作来部署模板？**

  可以，请参阅[使用 GitHub Actions 部署 Azure 资源管理器模板](deploy-github-actions.md)。

## <a name="next-steps"></a>后续步骤

有关 ARM 模板的简介，请参阅[什么是 ARM 模板？](overview.md)。
