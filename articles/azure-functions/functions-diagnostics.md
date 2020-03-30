---
title: Azure 功能诊断概述
description: 了解如何使用 Azure 函数诊断解决函数应用的问题。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834052"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure 函数诊断概述

运行函数应用时，需要为可能出现的任何问题做好准备，从 4xx 错误到触发故障。 Azure 函数诊断是一种智能交互式体验，可帮助您无需配置或额外费用解决功能应用故障。 当您遇到函数应用问题时，Azure 函数诊断会指出问题所在，以指导您获得正确的信息，以便更轻松、更快速地排除故障并解决问题。 本文介绍如何使用 Azure 函数诊断来更快地诊断和解决常见函数应用问题的基础知识。

## <a name="start-azure-functions-diagnostics"></a>启动 Azure 函数诊断

要访问 Azure 函数诊断：

1. 导航到[Azure 门户](https://portal.azure.com)中的函数应用。
2. 选择"**平台功能**"选项卡。
3. 选择"在**资源管理**下**诊断并解决问题**"，这将打开 Azure 函数诊断。
4. 使用主页磁贴中的关键字选择最能描述函数应用问题的类别。 您还可以在搜索栏中键入最能描述您的问题的关键字。 例如，您可以键入`execution`以查看与函数应用执行相关的诊断报告列表，并直接从主页打开它们。

![主页](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>使用交互式界面

选择最适合功能应用问题的主页类别后，Azure 函数诊断的交互式界面 Genie 可以指导您完成应用问题的诊断和解决。 您可以使用 Genie 提供的磁贴快捷方式查看您感兴趣的问题类别的完整诊断报告。 磁贴快捷方式提供了访问诊断指标的直接方法。

![精灵](./media/functions-diagnostics/genie.png)

选择磁贴后，可以看到与磁贴中描述的问题相关的主题列表。 这些主题提供了完整报告中的显著信息片段。 您可以选择其中任何一个主题以进一步调查问题。 此外，您还可以选择 **"查看完整报告**"以浏览单个页面上的所有主题。

![诊断报告预览](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

选择主题后，您可以查看特定于函数应用的诊断报告。 诊断报告使用状态图标来指示应用是否有任何特定问题。 您将看到问题的详细描述、建议的操作、相关指标和有用的文档。自定义诊断报告由在函数应用上运行的一系列检查生成。 诊断报告是一个有用的工具，用于查明函数应用中的问题，并指导您解决问题。

## <a name="find-the-problem-code"></a>查找问题代码

对于基于脚本的函数，可以使用**函数应用向下或报告错误**下的**函数执行和错误**来缩小导致异常或错误的代码行的范围。 此功能可以是获取根本原因和修复特定代码行问题的有用工具。 此选项不适用于预编译的 C# 和 Java 函数。

![函数执行错误的诊断报告](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![函数异常](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>后续步骤

您可以在[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)上提问或提供有关 Azure 功能诊断的反馈。 请在反馈`[Diag]`的标题中加入。

> [!div class="nextstepaction"]
> [监视功能应用](functions-monitoring.md)
