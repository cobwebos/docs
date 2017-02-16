---
title: "诊断 Azure 逻辑应用中的故障 | Microsoft Docs"
description: "了解逻辑应用出错的地方的常用方法"
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
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: f2a278beaf25fcc3c790bab802efea29463074af


---
# <a name="diagnosing-logic-app-failures"></a>诊断逻辑应用的故障
如果逻辑应用出现问题或故障，有几种方法可以帮助你更好地了解故障原因。  

## <a name="azure-portal-tools"></a>Azure 门户工具
Azure 门户提供了许多工具来诊断每个步骤中的每个逻辑应用。

### <a name="trigger-history"></a>触发器历史记录
每个逻辑应用至少有一个触发器。 如果你注意到应用未触发，那么可查找附加信息的第一个位置是触发器历史记录。 可以在逻辑应用的主边栏选项卡上访问触发器历史记录。

![查找触发器历史记录][1]

此操作列出了逻辑应用的所有触发尝试。 可以单击每个触发尝试以获取下一级别的详细信息（具体而言，就是触发尝试生成的任何输入或输出）。 如果看到任何失败的触发，请单击触发尝试，并深入到“输出”链接以查看生成的任何错误消息（例如，FTP 凭据无效）。

可能会看到以下不同状态：

* **忽略**。 对终结点进行轮询以检查数据，并收到没有数据可用的响应。
* **成功**。 触发器收到数据可用的响应。 此状态可能来自手动触发器、定期触发器或轮询触发器。 此状态可能伴随**已触发**状态，但是如果未满足代码视图中的条件或 SplitOn 命令，则不会有此状态。
* **失败**。 出现错误。

#### <a name="starting-a-trigger-manually"></a>手动启动触发器
如果希望逻辑应用立即检查（无需等待下一个周期）可用的触发器，则可以单击主边栏选项卡上的“选择触发器”来强制执行检查。 例如，如果单击此包含 Dropbox 触发器的链接，那么工作流将立即轮询 Dropbox 以获取新文件。

### <a name="run-history"></a>运行历史记录
被触发的每个触发器都会产生一次运行。 可以从主边栏选项卡中访问运行的信息，此边栏选项卡包含很多有助于了解工作流中发生的情况的信息。

![查找运行的历史记录][2]

运行显示为以下状态之一：

* **成功**。 所有操作都成功，或者曾出现故障，但是工作流中后来发生的操作处理了此故障。 也就是说，由设置为在失败操作后运行的操作处理了故障。
* **失败**。 至少有一个操作失败，并且之后工作流中的操作未处理此失败。
* **已取消**。 工作流正在运行，但收到取消请求。
* **正在运行**。 当前工作流正在运行。 出现此情况可能是因为工作流受限，也可能是因为当前的定价计划。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/app-service/plans/)中的操作限制。 配置诊断（在运行历史记录下面列出了图表）还可以提供有关正在进行的任何限制事件的信息。

如果正在查看运行历史记录，则可以从中获取更多详细信息。  

#### <a name="trigger-outputs"></a>触发器输出
触发器输出显示从触发器接收到的数据。 该输出可以帮助确定是否按预期返回所有属性。

> [!NOTE]
> 如果看见不了解的任何内容，那么此输出有助于了解逻辑应用功能如何[处理不同内容类型](../logic-apps/logic-apps-content-type.md)。
> 
> 

![触发器输出示例][3]

#### <a name="action-inputs-and-outputs"></a>操作输入和输出
可以深入到操作所接收的输入和输出信息。 这有助于了解输出的大小和形状，以及查看生成的任何错误消息。

![操作输入和输出][4]

## <a name="debugging-workflow-runtime"></a>调试工作流运行时
此功能除了监视运行的输入、输出和触发，还有助于在工作流中添加一些步骤以帮助调试。 [RequestBin](http://requestb.in) 是可以添加为工作流中的步骤的一个强大工具。 通过使用 RequestBin，可以设置 HTTP 请求检查器，从而确定 HTTP 请求的准确大小、形状和格式。 可以创建新的 RequestBin，并粘贴逻辑应用 HTTP POST 操作中的 URL 以及想要测试的正文内容（例如，一个表达式或另一个步骤的输出）。 运行逻辑应用后，可以刷新 RequestBin 查看请求在逻辑应用引擎中生成时的格式。

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png



<!--HONumber=Jan17_HO3-->


