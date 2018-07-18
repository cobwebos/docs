---
title: 排查和诊断故障 - Azure 逻辑应用 | Microsoft Docs
description: 了解逻辑应用发生故障的原因
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: b0bf6cd747860d938f80787d9bef6634a6a22d09
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441526"
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>排查和诊断逻辑应用故障

逻辑应用生成的信息有助于诊断和调试应用中的问题。 可以在 Azure 门户中通过查看工作流中的每个步骤来诊断逻辑应用。 或者，可以在工作流中增加一些步骤，进行运行时调试。

## <a name="review-trigger-history"></a>查看触发器历史记录

每个逻辑应用均从触发器开始。 如果未触发触发器，请首先查看触发器历史记录。 历史记录将列出逻辑应用进行的所有触发器尝试，以及关于每个触发器尝试的输入和输出详细信息。

1. 若要查看是否触发触发器，请选择逻辑应用菜单上的“概述”。 在“触发器历史记录”下，查看触发器状态。

   > [!TIP]
   > 如果看不到逻辑应用菜单，请尝试返回到 Azure 仪表板，然后重新打开逻辑应用。

   ![查看触发器历史记录](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * 如果找不到预期的数据，请尝试在工具栏中选择“刷新”。
   > * 如果列表显示许多触发器尝试，且你找不到所需条目，请尝试筛选列表。

   触发器尝试可能的状态如下：

   | 状态 | 说明 | 
   | ------ | ----------- | 
   | 成功 | 触发器已检查终结点并找到可用数据。 通常，此状态还会伴随出现“已触发”状态。 如果没有，触发器定义可能未满足某一条件或 `SplitOn` 命令。 <p>此状态可应用于手动触发器、定期触发器或轮询触发器。 如果操作生成未处理的错误，尽管可以成功运行触发器，但运行本身可能仍会失败。 | 
   | 已跳过 | 触发器已检查终结点，但未找到任何数据。 | 
   | 失败 | 发生错误。 若要查看失败触发器生成的任何错误消息，请选择该触发器尝试并选择“输出”。 例如，你可能发现输入无效。 | 
   ||| 

   你可能具有日期和事件相同的多条触发器记录，当逻辑应用找到多个项目时将发生这一情况。 
   每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来运行工作流。 默认情况下，每个实例并行运行，因此在启动运行前无需等待任何工作流。

   > [!TIP]
   > 可以重新检查触发器，无需等待下一个周期。 在“概述”工具栏中，选择“运行触发器”并选择该触发器，此操作将强制执行检查。 或者，在“逻辑应用设计器”工具栏中选择“运行”。

3. 若要检查触发器尝试的详细信息，请在“触发器历史记录”下选择该触发器尝试。 

   ![选择触发器尝试](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. 查看触发器生成的输入和任何输出。 触发器输出显示来自触发器的数据。 这些输出可以帮助确定是否按预期返回了所有属性。

   > [!NOTE]
   > 如果看到了无法理解的任何内容，请了解 Azure 逻辑应用[处理不同内容类型](../logic-apps/logic-apps-content-type.md)的方式。

   ![触发器输出](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>查看运行历史记录

一个已触发的触发器将启动一个工作流运行。 可查看该次运行过程中发生的情况，包括工作流中的每个步骤的状态，以及每个步骤的输入和输出。

1. 在逻辑应用菜单上选择“概述”。 在“运行历史记录”下，查看已触发触发器的运行。

   > [!TIP]
   > 如果看不到逻辑应用菜单，请尝试返回到 Azure 仪表板，然后重新打开逻辑应用。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * 如果找不到预期的数据，请尝试在工具栏中选择“刷新”。
   > * 如果列表显示多次运行，且你找不到所需条目，请尝试筛选列表。

   运行可能的状态如下：

   | 状态 | 说明 | 
   | ------ | ----------- | 
   | 成功 | 所有操作成功。 <p>如果特定操作发生故障，工作流中下面的操作将处理该故障。 | 
   | 失败 | 至少一个操作失败，并且工作流中未设置任何后续操作来处理该故障。 | 
   | 已取消 | 工作流正在运行，但收到取消请求。 | 
   | **正在运行** | 当前工作流正在运行。 <p>出现此状态的原因可能是工作流受限，或者当前定价计划方面的问题。 有关详细信息，请参阅[定价的操作限制页](https://azure.microsoft.com/pricing/details/logic-apps/)。 如果设置了[诊断日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md)，也可以获取发生的任何限制事件的有关信息。 | 
   ||| 

2. 查看某个特定运行中每个步骤的详细信息。 在“运行历史记录”下，选择要检查的运行。

   ![查看运行历史记录](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   运行本身成功或失败，“运行详细信息”视图将显示每个步骤，以及它们是成功还是失败。

   ![查看逻辑应用运行的详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. 若要检查输入、输出和特定步骤的任何错误消息，请选择该步骤，以便展开该形状并显示详细信息。 例如：

   ![查看步骤详细信息](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>执行运行时调试

若要帮助进行调试，可向工作流添加诊断步骤，同时查看触发器和运行历史记录。 例如，可以添加使用 [Webhook Tester](https://webhook.site/) 服务的步骤，以便可检查 HTTP 请求并确定其确切大小、形状和格式。

1. 请访问 [Webhook Tester](https://webhook.site/) 并复制创建的唯一 URL

2. 在逻辑应用中，添加包含想要测试的正文内容的 HTTP POST 操作（例如，某个表达式或另一个步骤的输出）。

3. 在 HTTP POST 操作中粘贴 Webhook Tester 的 URL。

4. 若要查看请求在从逻辑应用引擎生成时的格式，请运行逻辑应用并查看 Webhook Tester 了解详细信息。

## <a name="next-steps"></a>后续步骤

[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)
