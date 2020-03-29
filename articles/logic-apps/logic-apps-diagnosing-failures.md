---
title: 排查和诊断工作流故障
description: 了解如何在 Azure 逻辑应用中对工作流中的问题、错误和故障进行故障排除和诊断
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905085"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>在 Azure 逻辑应用中排查和诊断工作流故障

逻辑应用生成的信息有助于诊断和调试应用中的问题。 可以在 Azure 门户中通过查看工作流中的每个步骤来诊断逻辑应用。 或者，您可以将一些步骤添加到工作流以进行运行时调试。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>检查触发器历史记录

每个逻辑应用运行都以触发器尝试开始，因此，如果触发器未触发，请按照以下步骤操作：

1. [通过检查触发器历史记录来检查触发器](../logic-apps/monitor-logic-apps.md#review-trigger-history)的状态。 要查看有关触发器尝试的详细信息，请选择该触发器事件，例如：

   ![查看触发器状态和历史记录](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 检查触发器的输入，确认它们按预期显示。 在 **"输入"链接**下，选择显示 **"输入"** 窗格的链接。

   触发器输入包括触发器启动工作流所需的数据。 查看这些输入可以帮助您确定触发器输入是否正确，以及是否满足条件，以便工作流可以继续。

   例如，`feedUrl`此处的属性具有不正确的 RSS 源值：

   ![检查触发器输入是否存在错误](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 检查触发器输出（如果有）以确认它们按预期显示。 在 **"输出"链接**下，选择显示 **"输出"** 窗格的链接。

   触发器输出包括触发器传递到工作流下一步的数据。 查看这些输出可以帮助您确定正确的值还是预期值传递给工作流中的下一步，例如：

   ![查看触发器输出是否存在错误](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果发现任何无法识别的内容，请了解有关 Azure 逻辑应用中[不同内容类型](../logic-apps/logic-apps-content-type.md)的更多信息。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>检查运行历史记录

每次触发项目或事件时，逻辑应用引擎都会为每个项或事件创建并运行单独的工作流实例。 如果运行失败，请按照以下步骤查看该运行期间发生的情况，包括工作流中每个步骤的状态以及每个步骤的输入和输出。

1. 通过[检查运行历史记录检查](../logic-apps/monitor-logic-apps.md#review-runs-history)工作流的运行状态。 要查看有关失败运行的详细信息，包括在其状态下运行的所有步骤，请选择失败运行。

   ![查看运行历史记录并选择失败运行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 出现运行中的所有步骤后，展开第一个失败步骤。

   ![展开第一个失败步骤](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 检查失败步骤的输入，以确认它们是否按预期显示。

1. 查看某个特定运行中每个步骤的详细信息。 在“运行历史记录”下，选择要检查的运行****。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![查看逻辑应用运行的详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要检查输入、输出和特定步骤的任何错误消息，请选择该步骤，以便展开该形状并显示详细信息。 例如：

   ![查看步骤详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>执行运行时调试

为了帮助调试，您可以将诊断步骤添加到逻辑应用工作流，同时查看触发器并运行历史记录。 例如，可以添加使用 [Webhook Tester](https://webhook.site/) 服务的步骤，以便可检查 HTTP 请求并确定其确切大小、形状和格式。

1. 转到[Webhook 测试程序](https://webhook.site/)站点并复制生成的唯一 URL。

1. 在逻辑应用中，添加 HTTP POST 操作以及要测试的正文内容，例如表达式或其他步骤输出。

1. 将网址从 Webhook 测试仪粘贴到 HTTP POST 操作中。

1. 要查看从逻辑应用引擎生成请求时如何生成请求，请运行逻辑应用，然后重新访问 Webhook 测试程序网站以了解更多详细信息。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
