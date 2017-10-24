---
title: "诊断故障 - Azure 逻辑应用 | Microsoft 文档"
description: "识别逻辑应用故障的常用方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-logic-app-failures"></a>诊断逻辑应用故障
如果逻辑应用出现问题或故障，有几种方法可帮助更好地了解故障原因。  

## <a name="azure-portal-tools"></a>Azure 门户工具
Azure 门户提供了许多工具来诊断每个步骤中的每个逻辑应用。

### <a name="trigger-history"></a>触发器历史记录

每个逻辑应用至少有一个触发器。 如果注意到应用未触发，请先查看触发器历史记录了解详细信息。 可在逻辑应用的主边栏选项卡上访问触发器历史记录。

![查找触发器历史记录][1]

触发器历史记录列出了逻辑应用尝试的所有触发操作。 可以单击尝试的每个触发操作深入到详细信息，具体而言，即尝试的触发操作生成的任何输入或输出。 如果找到了失败的触发器，请选择尝试的触发操作，并选择“输出”链接查看生成的任何错误消息（例如，FTP 凭据无效）。

可能会看到以下不同状态：

* **忽略**。 已轮询终结点以检查数据，并收到没有可用数据的响应。
* **成功**。 触发器收到数据可用的响应。 此状态可能是手动触发器、定期触发器或轮询触发器造成的。 此状态通常伴随“已触发”状态，但如果未满足代码视图中的条件或 SplitOn 命令，则不会出现此状态。
* **失败**。 出现错误。

#### <a name="start-a-trigger-manually"></a>手动启动触发器

如果希望逻辑应用立即检查（无需等待下一个周期）可用的触发器，可以单击主边栏选项卡上的“选择触发器”来强制执行检查。 例如，如果单击此包含 Dropbox 触发器的链接，工作流将立即轮询 Dropbox 以获取新文件。

### <a name="run-history"></a>运行历史记录

被触发的每个触发器都会产生一次运行。 可以从主边栏选项卡中访问运行信息，其中包含很多有助于了解工作流中发生的情况的详细信息。

![查找运行的历史记录][2]

运行显示为以下状态之一：

* **成功**。 所有操作成功。 如果曾发生故障，工作流中后来发生的操作已处理该故障。 也就是说，由设置为在失败操作后运行的操作已处理故障。
* **失败**。 至少有一个操作失败，并且之后工作流中的操作未处理此失败。
* **已取消**。 工作流正在运行，但收到取消请求。
* **正在运行**。 当前工作流正在运行。 出现此状态的原因可能是工作流受限，或者当前定价计划方面的问题。 有关详细信息，请参阅[定价页中的操作限制](https://azure.microsoft.com/pricing/details/app-service/plans/)。 配置诊断（运行历史记录下面显示的图表）还可以提供有关正在发生的任何限制事件的信息。

如果正在查看运行历史记录，则可以从中获取更多详细信息。  

#### <a name="trigger-outputs"></a>触发器输出

触发器输出显示来自触发器的数据。 这些输出可以帮助确定是否按预期返回了所有属性。

> [!NOTE]
> 如果看到了无法理解的任何内容，请了解 Azure 逻辑应用如何[处理不同内容类型](../logic-apps/logic-apps-content-type.md)。
> 

![触发器输出示例][3]

#### <a name="action-inputs-and-outputs"></a>操作输入和输出

可以深入到操作所接收的输入和输出信息。 这些数据有助于了解输出的大小和形状，以及查找可能生成的任何错误消息。

![操作输入和输出][4]

## <a name="debug-workflow-runtime"></a>调试工作流运行时

除了监视运行的输入、输出和触发器以外，还可以在工作流中添加一些可帮助调试的步骤。 
[RequestBin](http://requestb.in) 是可以添加为工作流中的步骤的一个强大工具。 通过使用 RequestBin，可以设置 HTTP 请求检查器，从而确定 HTTP 请求的准确大小、形状和格式。 可以创建 RequestBin，并粘贴逻辑应用 HTTP POST 操作中的 URL 以及想要测试的正文内容（例如，某个表达式或另一个步骤的输出）。 运行逻辑应用后，可以刷新 RequestBin，查看请求在逻辑应用引擎中生成时的格式。

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png
