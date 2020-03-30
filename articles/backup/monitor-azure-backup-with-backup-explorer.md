---
title: 使用备份资源管理器监视备份
description: 本文介绍如何使用备份资源管理器对跨保管库、订阅、区域和租户的备份执行实时监视。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131808"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用备份资源管理器监视备份

随着组织将越来越多的计算机备份到云中，高效监视这些备份变得越来越重要。 最好的开始方法是使用中心位置查看大型庄园中的操作信息。

备份资源管理器是一个内置的 Azure 监视器工作簿，它为 Azure 备份客户提供此单个中心位置。 备份资源管理器可帮助您监视 Azure 上整个备份庄园的操作活动，包括租户、位置、订阅、资源组和保管库。 广义而言，备份资源管理器提供以下功能：

* **规模透视**：获取尚未为整个庄园的备份项、作业、警报、策略和资源配置的备份项、作业、警报、策略和资源的聚合视图。 
* **向下钻取分析**：在一个位置显示有关每个作业、警报、策略和备份项的详细信息。
* **可操作接口**：确定问题后，可以通过无缝地访问相关的备份项或 Azure 资源来解决它。

这些功能通过与 Azure 资源图和 Azure 监视器工作簿的本机集成提供开箱即用。

> [!NOTE]
> * 备份资源管理器当前仅适用于 Azure 虚拟机 （VM） 数据。
> * 备份资源管理器旨在作为一个操作仪表板，用于查看过去 7 天（最大）备份的信息。
> * 备份资源管理器当前不受国家云的支持。
> * 目前，不支持自定义备份资源管理器模板。 
> * 我们不建议在 Azure 资源图数据上编写自定义自动化。

## <a name="get-started"></a>入门

您可以通过访问任何恢复服务保管库并在 **"概述"** 窗格中选择 **"备份资源管理器"** 链接来访问备份资源管理器。

![保险库快速链接](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

选择链接将打开"备份资源管理器"，该资源管理器可在您有权访问的所有保管库和订阅之间提供聚合视图。 如果使用 Azure 灯塔帐户，则可以查看有权访问的所有租户的数据。 有关详细信息，请参阅本文末尾的"交叉租户视图"部分。

![备份资源管理器着陆页](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>备份资源管理器用例

备份资源管理器显示多个选项卡，每个选项卡提供有关特定备份项目的详细信息（例如，备份项、作业或策略）。 本节简要概述了每个选项卡。 这些视频为每个备份工件提供了示例用例，以及可用控件的说明。

### <a name="the-summary-tab"></a>"摘要"选项卡

"**摘要"** 选项卡可快速浏览备份区的总体状况。 例如，您可以查看受保护的项数、尚未启用保护的项目数或过去 24 小时内成功访问的作业数。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>"备份项目"选项卡

您可以按订阅、保管库和其他特征筛选和查看每个备份项目。 通过选择备份项的名称，可以打开该项的 Azure 窗格。 例如，在表中，您可能会注意到项目*X*的最后一次备份失败。通过选择*X*，可以打开项目的 **"备份"** 窗格，您可以在其中触发按需备份操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>"作业"选项卡

选择 **"作业"** 选项卡以查看过去 7 天内触发的所有作业的详细信息。 在这里，您可以按*作业操作*、*作业状态*和*错误代码*（对于失败的作业）进行筛选。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>"警报"选项卡

选择 **"警报"** 选项卡可查看过去 7 天内在保管库上生成的所有警报的详细信息。 您可以按类型（*备份失败*或*还原失败*）、当前状态（*活动*或*已解决*）和严重性（*严重*、*警告*或*信息*）筛选警报。 您还可以选择一个链接以转到 Azure VM 并采取任何必要的操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>"策略"选项卡

您可以选择 **"策略"** 选项卡以查看有关在备份区中创建的所有备份策略的关键信息。 您可以查看与每个策略关联的项数，以及策略指定的保留范围和备份频率。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>"未启用备份"选项卡

应为所有需要保护的计算机启用备份。 使用备份资源管理器，备份管理员可以快速识别组织中哪些计算机尚未受到备份的保护。 要查看此信息，请选择"**未启用备份**"选项卡。

"**未启用备份"** 窗格显示一个表，其中列出了未受保护的计算机。 您的组织可能会为生产计算机和测试计算机或服务各种功能的计算机分配不同的标记。 由于每类计算机都需要单独的备份策略，因此按标记进行筛选可帮助您查看特定于每个类别的信息。 选择任何计算机的名称会将您重定向到该计算机的 **"配置备份"** 窗格，您可以选择应用适当的备份策略。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>导出到 Excel

您可以将任何表或图表的内容导出为 Excel 电子表格。 内容按现在导出，并应用现有筛选器。 要导出其他表行，可以使用每个选项卡顶部的 **"每页行**"下拉列表增加要在页面上显示的行数。

## <a name="pin-to-the-dashboard"></a>固定到仪表板

您可以选择每个表或图表顶部的"pin"图标，将其固定到 Azure 门户仪表板。 固定此信息可帮助您创建自定义仪表板，该仪表板可定制以显示对您最重要的信息。

## <a name="cross-tenant-views"></a>交叉租户视图

如果您是 Azure 灯塔用户，具有对跨多个租户环境的订阅的委派访问权限，则可以使用默认订阅筛选器。 通过选择 Azure 门户右上角的"筛选器"图标，显示要查看数据的订阅。 使用此功能时，备份资源管理器会聚合有关所选订阅中所有保管库的信息。 要了解更多信息，请参阅[什么是 Azure 灯塔？](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure 监视器获取备份数据的见解](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
