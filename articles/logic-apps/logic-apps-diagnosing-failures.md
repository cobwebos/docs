---
title: 排查和诊断工作流故障
description: 了解如何排查和诊断 Azure 逻辑应用工作流中的问题、错误与故障
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76905085"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>在 Azure 逻辑应用中排查和诊断工作流故障

逻辑应用生成的信息有助于诊断和调试应用中的问题。 可以在 Azure 门户中通过查看工作流中的每个步骤来诊断逻辑应用。 或者，可以在工作流中增加一些步骤进行运行时调试。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>检查触发历史记录

每个逻辑应用运行都以触发尝试开始，如果触发器不激发，请执行以下步骤：

1. 通过[检查触发历史记录](../logic-apps/monitor-logic-apps.md#review-trigger-history)来检查触发器的状态。 若要查看有关触发尝试的详细信息，请选择该触发事件，例如：

   ![查看触发器状态和历史记录](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. 检查触发器的输入，确认它们按预期方式显示。 在“输入链接”下，选择相应的链接显示“输入”窗格。  

   触发器输入包括启动工作流时触发器预期需要的数据。 检查这些输入有助于确定触发器输入是否正确，以及是否满足条件，使工作流能够继续。

   例如，以下 `feedUrl` 属性使用了错误的 RSS 源值：

   ![检查触发器输入中的错误](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. 检查触发器的输出（如果有），确认它们按预期方式显示。 在“输出链接”下，选择相应的链接显示“输出”窗格。  

   触发器输出包括触发器传递给工作流中下一步骤的数据。 检查这些输出有助于确定是否已将正确或预期的值传递给工作流中的下一步骤，例如：

   ![检查触发器输出中的错误](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 如果发现有任何无法识别的内容，请详细了解 Azure 逻辑应用中[不同的内容类型](../logic-apps/logic-apps-content-type.md)。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>检查运行历史记录

每次针对某个项或事件激发触发器时，逻辑应用引擎将针对每个项或事件创建并运行一个独立的工作流实例。 如果某个运行失败，请执行以下步骤检查该次运行过程中发生的情况，包括工作流中每个步骤的状态，以及每个步骤的输入和输出。

1. 通过[检查运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)来检查工作流的运行状态。 若要查看有关某个失败运行的详细信息（包括该运行中的步骤及其状态），请选择该运行。

   ![查看运行历史记录并选择失败的运行](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 显示该运行中的所有步骤后，展开第一个失败的步骤。

   ![展开第一个失败的步骤](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 检查失败步骤的输入，确认它们是否按预期方式显示。

1. 查看某个特定运行中每个步骤的详细信息。 在“运行历史记录”下，选择要检查的运行  。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![查看逻辑应用运行的详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 若要检查输入、输出和特定步骤的任何错误消息，请选择该步骤，以便展开该形状并显示详细信息。 例如：

   ![查看步骤详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>执行运行时调试

若要帮助进行调试，可向逻辑应用工作流添加诊断步骤，同时查看触发器和运行历史记录。 例如，可以添加使用 [Webhook Tester](https://webhook.site/) 服务的步骤，以便可检查 HTTP 请求并确定其确切大小、形状和格式。

1. 转到 [Webhook Tester](https://webhook.site/) 站点，复制生成的唯一 URL。

1. 在逻辑应用中，添加一个 HTTP POST 操作以及想要测试的正文内容，例如，某个表达式或另一个步骤的输出。

1. 将 Webhook Tester 中的 URL 粘贴到该 HTTP POST 操作中。

1. 若要查看在从逻辑应用引擎生成请求时如何构建该请求，请运行逻辑应用，然后重新访问 Webhook Tester 站点了解更多详细信息。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
