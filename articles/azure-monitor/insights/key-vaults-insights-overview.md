---
title: 监视 Key Vault Azure Monitor Key Vault （预览版） |Microsoft Docs
description: 本文介绍密钥保管库的 Azure Monitor。
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211298"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>监视 key vault 服务与 Key Vault 的 Azure Monitor （预览）
Azure Monitor for Key Vault （预览版）提供了对你的密钥保管库的全面监视，同时提供了你的 Key Vault 请求、性能、故障和延迟的统一视图。
本文将帮助你了解如何载入和自定义 Key Vault （预览版）的 Azure Monitor 体验。

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Key Vault 的 Azure Monitor 简介（预览版）

在转到体验之前，你应该了解它是如何呈现和可视化信息的。
-    基于请求、失败细目以及操作和延迟概述，**按比例透视**显示性能的快照视图。
-   向**下钻取**特定密钥保管库的分析以执行详细分析。
-    可**自定义**，你可以在其中更改要查看的指标、修改或设置与限制相符的阈值，并保存你自己的工作簿。 工作簿中的图表可以固定到 Azure 仪表板。

Key Vault 的 Azure Monitor 结合了日志和指标来提供全局监视解决方案。 所有用户都可以访问基于指标的监视数据，但包含基于日志的可视化效果可能要求用户[启用其 Azure Key Vault 日志记录](https://docs.microsoft.com/azure/key-vault/key-vault-logging)。

## <a name="configuring-your-key-vaults-for-monitoring"></a>配置用于监视的密钥保管库

> [!NOTE]
> 启用日志是一项付费服务，提供其他监视功能。

1. "操作 & 延迟" 选项卡可帮助你确定启用了多少个密钥保管库。 若要开始收集，请选择 "**启用**" 按钮，该按钮会显示一个单独的工作簿，其中列出了需要启用诊断日志的密钥保管库。

    ![显示 "蓝色启用" 按钮的 "操作和延迟" 选项卡的屏幕截图](./media/key-vaults-insights-overview/enable-logging.png)

2. 若要启用诊断日志，请单击 "操作" 列下的 "**启用**" 链接，然后创建一个将日志发送到 Log Analytics 工作区的新诊断设置。 建议将所有日志发送到同一工作区。

3. 保存诊断设置后，你将能够查看 Key Vault 见解下的所有基于日志的图表和可视化效果。 请注意，可能需要几分钟到几分钟才能开始填充日志。

4. 有关如何为 Key Vault 服务启用诊断日志的其他帮助，请阅读[完整指南](https://docs.microsoft.com/azure/key-vault/key-vault-logging)。

## <a name="view-from-azure-monitor"></a>从 Azure Monitor 查看

从 Azure Monitor 中，可以查看订阅中多个密钥保管库的请求、延迟和失败详细信息，并帮助确定性能问题和限制方案。

若要查看所有订阅中的存储帐户的使用情况和操作，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)

2. 从 "Azure 门户中的左窗格中选择"**监视器**"，然后在" Insights "部分，选择"**密钥保管库（预览版）**"。

![具有多个图形的概述体验的屏幕截图](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>概述工作簿

在所选订阅的概述工作簿中，表显示了在订阅中分组的密钥保管库的交互式密钥保管库指标。 您可以根据从下列下拉列表中选择的选项筛选结果：

* 订阅–只列出具有密钥保管库的订阅。

* Key vault –默认情况下，最多只能预选择5个密钥保管库。 如果在作用域选择器中选择 "全部" 或 "多个密钥保管库"，则将返回最多200个密钥保管库。 例如，如果你选择的三个订阅中共有573个密钥保管库，则只会显示200个保管库。

* 时间范围–默认情况下，基于所做的相应选择显示最近24小时内的信息。

计数器磁贴在下拉列表下汇总所选订阅中的密钥保管库总数，并反映所选的数目。 对于报表请求、失败和延迟指标的工作簿的列，有条件颜色编码的热图。 最深的颜色具有最高的值，较浅的颜色基于最低值。

## <a name="failures-workbook"></a>失败工作簿

选择页面顶部的 "**失败**"，"失败" 选项卡将打开。 其中显示了 API 命中率、一段时间的频率以及某些响应代码的数量。

![故障工作簿的屏幕截图](./media/key-vaults-insights-overview/failures.png)

工作簿中的列有条件颜色编码或热图，用于报告 API 使用蓝色值的度量值。 最深的颜色具有最高的值，较浅的颜色基于最低值。

工作簿显示成功（2xx 状态代码）、身份验证错误（401/403 状态代码）、限制（429状态代码）和其他失败（4xx 状态代码）。

为了更好地了解每个状态代码表示的内容，我们建议阅读有关[Azure Key Vault 状态和响应代码](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses)的文档。

## <a name="operations--latency-workbook"></a>操作 & 延迟工作簿

在页面顶部选择 "**操作 & 延迟**"，"**操作" & 延迟**"选项卡将打开。 此选项卡可让你将密钥保管库加入监视。 有关更多详细步骤，请参阅[配置用于监视的密钥保管库](#configuring-your-key-vaults-for-monitoring)部分。

你可以看到为日志记录启用了多少个密钥保管库。 如果至少有一个保管库配置正确，你将能够看到显示每个密钥保管库的操作和状态代码的表。 您可以单击行的详细信息部分，以获取有关各个操作的其他信息。

![操作和延迟图的屏幕截图](./media/key-vaults-insights-overview/logs.png)

如果未看到此部分的任何数据，请参考有关如何为 Azure Key Vault 启用日志的顶部部分，或查看下面的故障排除部分。

## <a name="view-from-a-key-vault-resource"></a>从 Key Vault 资源查看

若要直接从密钥保管库访问 Key Vault 的 Azure Monitor，请执行以下操作：

1. 在 Azure 门户中，选择 "Key vault"。

2. 从列表中选择一个密钥保管库。 在 "监视" 部分，选择 "见解（预览版）"。

还可以通过从 Azure Monitor 级别工作簿中选择密钥保管库的资源名称来访问这些视图。

![Key vault 资源视图的屏幕截图](./media/key-vaults-insights-overview/key-vault-resource-view.png)

在密钥保管库的**概述**工作簿中，它会显示多个性能指标，可帮助你快速评估：

- 交互式性能图表，显示与密钥保管库事务、延迟和可用性相关的最重要的详细信息。

- 指标和状态磁贴突出显示服务可用性、密钥保管库资源的事务总数和总体延迟。

为**失败**或**操作**选择任何其他选项卡会打开各自的工作簿。

![故障视图的屏幕截图](./media/key-vaults-insights-overview/resource-failures.png)

"失败" 工作簿将细分所选时间范围内所有 key vault 请求的结果，并提供成功（2xx）、身份验证错误（401/403）、限制（429）和其他失败的分类。

![操作视图的屏幕截图](./media/key-vaults-insights-overview/operations.png)

操作工作簿允许用户深入了解所有事务的完整详细信息，可以使用顶层磁贴按结果状态筛选这些事务。

![操作视图的屏幕截图](./media/key-vaults-insights-overview/info.png)

用户还可以根据上部表中的特定事务类型来确定视图的作用域，这会动态更新下表，用户可以在弹出窗口上下文窗格中查看完整操作的详细信息。

>[!NOTE]
> 请注意，用户必须启用了诊断设置才能查看此工作簿。 若要详细了解如何启用诊断设置，请阅读有关[Azure Key Vault 日志记录](https://docs.microsoft.com/azure/key-vault/general/logging)的详细信息。

## <a name="pin-and-export"></a>固定和导出

您可以通过选择部分右上角的图钉图标将任一指标部分固定到 Azure 仪表板。

多订阅和密钥保管库概述或失败工作簿支持通过选择图钉图标左侧的 "下载" 图标来导出 Excel 格式的结果。

![选定的固定图标的屏幕截图](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>自定义 Key Vault 的 Azure Monitor

本部分重点介绍编辑工作簿以自定义以支持数据分析需求的常见方案：
*  将工作簿的范围限定为始终选择特定订阅或密钥保管库
* 更改网格中的度量值
* 更改请求阈值
* 更改颜色渲染

您可以通过从顶部工具栏选择 "**自定义**" 按钮来启用编辑模式，开始自定义。

!["自定义" 按钮的屏幕截图](./media/key-vaults-insights-overview/customize.png)

自定义项保存到自定义工作簿，以防止覆盖已发布工作簿中的默认配置。 工作簿保存在资源组中，无论是在专用于你的我的报表节中，还是在有权访问资源组的每个人均可访问的 "共享报表" 部分中。 保存自定义工作簿后，需要先进入工作簿库以启动它。

![工作簿库的屏幕截图](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>指定订阅或密钥保管库

可以通过执行以下步骤，将多订阅和密钥保管库概述或失败工作簿配置为每次运行时的特定订阅或密钥保管库的作用域：

1. 从门户中选择 "**监视器**"，然后从左侧窗格中选择 "**密钥保管库（预览版）** "。
2. 在**概述**工作簿上，从命令栏中选择 "**编辑**"。
3. 从 "**订阅**" 下拉列表中选择要使用的一个或多个订阅作为默认订阅。 请记住，工作簿最多支持选择10个订阅。
4. 从 "**密钥保管库**" 下拉列表中选择一个或多个要用作默认帐户的帐户。 请记住，工作簿最多支持选择200个存储帐户。
5. 选择命令栏中的 "**另存为**"，以保存工作簿的副本和自定义项，然后单击 "**完成编辑**" 以返回到读取模式。

## <a name="troubleshooting"></a>疑难解答

本部分将帮助你诊断和排查在使用 Azure Monitor Key Vault （预览版）时可能遇到的一些常见问题。 使用下面的列表来查找与具体问题相关的信息。

### <a name="resolving-performance-issues-or-failures"></a>解决性能问题或故障

若要帮助解决你在 Key Vault （预览版）中 Azure Monitor 标识的任何 key vault 相关问题，请参阅[Azure Key Vault 文档](https://docs.microsoft.com/azure/key-vault/)。

### <a name="why-can-i-only-see-200-key-vaults"></a>为什么只能看到200密钥保管库？

可以选择和查看200密钥保管库。 无论选择的订阅数量如何，所选密钥保管库的数目限制为200。

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>单击固定的项将发生什么情况？

单击仪表板上的固定项后，它将打开以下两个内容之一：
* 如果已保存见解，则会打开保存了 pin 的 insights 实例。
* 如果见解未保存，则会打开新的默认 insights 实例。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>为什么我在订阅选取器中看不到所有的订阅？

我们只显示包含从所选订阅筛选器中选择的密钥保管库的订阅，该筛选器在 Azure 门户标头的 "目录 + 订阅" 中选择。

![订阅筛选器的屏幕截图](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>我收到一条错误消息，指出 "查询超出了允许的工作区/区域的最大数目"，现在该怎么办？

目前，只有25个区域和200个工作区有限制，若要查看你的数据，你将需要减少订阅和/或资源组的数目。

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>我想要进行更改或添加其他可视化效果，以 Key Vault 见解，如何实现此目的？

若要进行更改，请选择 "编辑模式" 以修改工作簿，然后可以将工作保存为绑定到指定订阅和资源组的新工作簿。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>固定工作簿的任何部分后，时间粒度是多少？

我们使用 "自动" 时间粒度，因此，它取决于选择的时间范围。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>工作簿的任何部分固定时的时间范围是多少？

时间范围将取决于仪表板设置。

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>为什么在 "操作" & 延迟 "部分下看不到我的 Key Vault 的任何数据？

若要查看基于日志的数据，需要为要监视的每个密钥保管库启用日志。 可在每个密钥保管库的诊断设置下完成此操作。 需要将数据发送到指定的 Log Analytics 工作区。

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>我已为我的 Key Vault 启用了日志，为什么在操作 & 延迟时仍无法看到我的数据？

目前，诊断日志无法以追溯方式，因此，只有在对密钥保管库执行了操作后，才会显示数据。 因此，可能需要一段时间，范围从几小时到一天，具体取决于密钥保管库的活动情况。

此外，如果选择了大量的密钥保管库和订阅，则由于查询限制，可能无法查看数据。 若要查看数据，可能需要减少所选订阅或密钥保管库的数目。 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>如果我想要查看其他数据或进行我自己的可视化效果，该怎么办？ 如何对 Key Vault Insights 进行更改？

您可以通过使用编辑模式来编辑现有工作簿，然后将您的工作保存为新的工作簿，该工作簿将包含所有新的更改。

## <a name="next-steps"></a>后续步骤

了解工作簿设计为支持的方案、如何创作新报表和自定义现有报表，以及如何通过查看[使用 Azure Monitor 工作簿创建交互式报表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
