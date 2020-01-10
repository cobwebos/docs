---
title: Azure Functions 诊断概述
description: 了解如何通过 Azure Functions 诊断来排查 function app 的问题。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834052"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 诊断概述

当你运行函数应用时，你想要为任何可能出现的问题做好准备，从4xx 错误到触发失败。 Azure Functions 诊断是一种智能交互式体验，可帮助你在不配置或额外成本的情况中排除 function 应用的故障。 当你在 function app 中遇到问题时，Azure Functions 的诊断指出了错误的信息，以指导你更轻松、更快速地排查和解决问题。 本文介绍如何使用 Azure Functions 诊断，以更快地诊断和解决常见的函数应用问题。

## <a name="start-azure-functions-diagnostics"></a>启动 Azure Functions 诊断

若要访问 Azure Functions 诊断：

1. 在[Azure 门户](https://portal.azure.com)中导航到 function app。
2. 选择 "**平台功能**" 选项卡。
3. 选择 "**诊断和解决** **资源管理**" 下的问题，这会打开 Azure Functions 诊断。
4. 使用 "主页" 磁贴中的关键字，选择最能描述函数应用问题的类别。 你还可以在搜索栏中键入最能描述你的问题的关键字。 例如，你可以键入 `execution` 来查看与 function app 执行相关的诊断报告列表，并直接从主页打开它们。

![主页](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>使用交互式接口

选择最适合您的函数应用程序问题的主页类别后，Azure Functions 诊断 "交互式界面精灵，可以指导您完成应用程序的诊断和解决问题。 你可以使用精灵提供的磁贴快捷方式查看你感兴趣的问题类别的完整诊断报告。 磁贴快捷方式为您提供了一种访问诊断指标的直接方式。

![精灵](./media/functions-diagnostics/genie.png)

选择磁贴后，可以看到与磁贴中所述问题相关的主题列表。 这些主题提供完整报告的重要信息片段。 您可以选择这些主题中的任何一种，以进一步调查问题。 此外，还可以选择 "**查看完整报表**" 来浏览单个页面上的所有主题。

![诊断报告预览](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

选择主题后，可以查看特定于 function app 的诊断报告。 诊断报告使用状态图标来指示应用是否有任何特定问题。 你将看到有关问题的详细描述、建议操作、相关度量和有用文档。自定义的诊断报告是从对函数应用运行的一系列检查生成的。 诊断报告可能是一个有用的工具，可用于在 function app 中查明问题并指导你解决问题。

## <a name="find-the-problem-code"></a>查找问题代码

对于基于脚本的函数，可以在**Function App 关闭或报告错误**的情况下使用**函数执行和错误**，缩小导致异常或错误的代码行。 此功能可能是一个有用的工具，可用于从特定代码行获取根本原因并解决问题。 此选项不适用于预编译C#和 Java 函数。

![有关函数执行错误的诊断报告](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![函数异常](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>后续步骤

你可以提出问题，或在[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)Azure Functions 诊断提供反馈。 请在反馈的标题中加入 `[Diag]`。

> [!div class="nextstepaction"]
> [监视函数应用](functions-monitoring.md)
