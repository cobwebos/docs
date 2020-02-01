---
title: 排查和诊断工作流故障
description: 了解如何在 Azure 逻辑应用中对工作流中的问题、错误和失败进行故障排除和诊断
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905085"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>在 Azure 逻辑应用中排查和诊断工作流故障

逻辑应用生成的信息有助于诊断和调试应用中的问题。 可以在 Azure 门户中通过查看工作流中的每个步骤来诊断逻辑应用。 或者，可以将一些步骤添加到工作流，以便进行运行时调试。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>检查触发器历史记录

每个逻辑应用的运行都以触发器尝试开始，因此，如果触发器不触发，请遵循以下步骤：

1. 通过[检查触发器历史记录](../logic-apps/monitor-logic-apps.md#review-trigger-history)来检查触发器的状态。 若要查看有关触发器尝试的详细信息，请选择该触发器事件，例如：

   ![查看触发器状态和历史记录](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 检查触发器的输入，确认它们按预期方式出现。 在 "**输入链接**" 下，选择显示 "**输入**" 窗格的链接。

   触发器输入包含触发器预期的数据，并需要启动工作流。 查看这些输入可帮助您确定触发器输入是否正确，以及是否满足条件以使工作流继续进行。

   例如，此处 `feedUrl` 属性具有错误的 RSS 源值：

   ![查看错误的触发器输入](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 检查触发器输出（如果有）以确认它们按预期显示。 在 "**输出链接**" 下，选择显示 "**输出**" 窗格的链接。

   触发器输出包含触发器传递到工作流中下一步的数据。 查看这些输出可以帮助您确定是否将正确或预期的值传递到工作流中的下一步，例如：

   ![查看触发器的错误输出](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果找到不能识别的任何内容，请在 Azure 逻辑应用中详细了解[不同的内容类型](../logic-apps/logic-apps-content-type.md)。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>检查运行历史记录

每次触发某个项或事件的触发器时，逻辑应用引擎将为每个项或事件创建并运行一个单独的工作流实例。 如果运行失败，请按照以下步骤查看在运行过程中所发生的情况，包括工作流中每个步骤的状态以及每个步骤的输入和输出。

1. 检查[运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)，检查工作流的运行状态。 若要查看有关失败的运行的详细信息（包括在其状态下运行的所有步骤），请选择失败的运行。

   ![查看运行历史记录并选择失败的运行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 在运行中的所有步骤出现后，展开第一个失败的步骤。

   ![展开第一个失败的步骤](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 检查失败步骤的输入，确认它们是否按预期方式显示。

1. 查看某个特定运行中每个步骤的详细信息。 在“运行历史记录”下，选择要检查的运行。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![查看逻辑应用运行的详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要检查输入、输出和特定步骤的任何错误消息，请选择该步骤，以便展开该形状并显示详细信息。 例如：

   ![查看步骤详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>执行运行时调试

若要帮助进行调试，可以将诊断步骤添加到逻辑应用工作流，以及查看触发器和运行历史记录。 例如，可以添加使用 [Webhook Tester](https://webhook.site/) 服务的步骤，以便可检查 HTTP 请求并确定其确切大小、形状和格式。

1. 请中转到[Webhook 测试人员](https://webhook.site/)站点并复制生成的唯一 URL。

1. 在逻辑应用中，添加 HTTP POST 操作以及想要测试的正文内容（例如，表达式或其他步骤输出）。

1. 将你的 URL 从 Webhook 测试人员粘贴到 HTTP POST 操作中。

1. 若要查看从逻辑应用引擎生成请求时如何生成请求，请运行逻辑应用，并重新查看 Webhook 测试人员站点以了解更多详细信息。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
