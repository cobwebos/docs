---
title: Azure Functions 诊断概述
description: 了解如何通过 Azure Functions 诊断来排查 function app 的问题。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122353"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 诊断概述

当你运行函数应用时，你想要为任何可能出现的问题做好准备，从4xx 错误到触发失败。 Azure Functions 诊断是一种智能交互式体验，可帮助你在不配置或额外成本的情况中排除 function 应用的故障。 当你在 function app 中遇到问题时，Azure Functions 诊断指出了什么问题。 它将指导您找到正确的信息，以便更轻松快速地进行故障排除并解决问题。 本文介绍如何使用 Azure Functions 诊断，以更快地诊断和解决常见的函数应用问题。

## <a name="start-azure-functions-diagnostics"></a>启动 Azure Functions 诊断

开始 Azure Functions 诊断：

1. 在[Azure 门户](https://portal.azure.com)中导航到 function app。
1. 选择 "**诊断并解决问题**" 打开 Azure Functions 诊断。
1. 使用 "主页" 磁贴中的关键字，选择最能描述函数应用问题的类别。 你还可以在搜索栏中键入最能描述你的问题的关键字。 例如，你可以键入 `execution` 以查看与 function app 执行相关的诊断报告列表，并直接从主页打开它们。

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="搜索 Azure Functions 诊断。" border="true":::

## <a name="use-the-interactive-interface"></a>使用交互式接口

选择最适合你的函数应用的问题的主页类别后，Azure Functions 诊断 "交互式界面（名为精灵）可以指导你完成应用的诊断和解决问题。 你可以使用精灵提供的磁贴快捷方式查看你感兴趣的问题类别的完整诊断报告。 磁贴快捷方式为您提供了一种访问诊断指标的直接方式。

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="精灵是 Azure Functions 诊断 "界面。" border="false":::

选择磁贴后，可以看到与磁贴中所述问题相关的主题列表。 这些主题提供完整报告的重要信息片段。 选择这些主题中的任何一种可以进一步调查问题。 此外，还可以选择 "**查看完整报表**" 来浏览单个页面上的所有主题。

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="诊断报告预览" border="false":::

## <a name="view-a-diagnostic-report"></a>查看诊断报告

选择主题后，可以查看特定于 function app 的诊断报告。 诊断报告使用状态图标来指示应用是否有任何特定问题。 你将看到有关问题的详细描述、建议操作、相关度量和有用文档。自定义的诊断报告是从对函数应用运行的一系列检查生成的。 诊断报告可能是一个有用的工具，可用于在 function app 中查明问题并指导你解决问题。

## <a name="find-the-problem-code"></a>查找问题代码

对于基于脚本的函数，可以在**Function App 关闭或报告错误**的情况下使用**函数执行和错误**，缩小导致异常或错误的代码行。 您可以使用此工具来获取根本原因并解决特定代码行中的问题。 此选项不适用于预编译的 c # 和 Java 函数。

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="有关函数执行错误的诊断报告" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="异常详细信息的视图。" border="false":::

## <a name="next-steps"></a>后续步骤

你可以提出问题，或在[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)Azure Functions 诊断提供反馈。 `[Diag]`在反馈的标题中包含。

> [!div class="nextstepaction"]
> [监视函数应用](functions-monitoring.md)
