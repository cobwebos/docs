---
title: 从 Azure Application Insights 导出到 Power BI | Microsoft Docs
description: 可以在 Power BI 中显示分析查询。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294116"
---
# <a name="feed-power-bi-from-application-insights"></a>从 Application Insights 向 Power BI 馈送数据
[Power BI](http://www.powerbi.com/) 是一套商业工具，可帮助分析数据及分享见解。 每个设备上都提供了丰富的仪表板。 可以结合许多源的数据，包括来自 [Azure Application Insights](app-insights-overview.md) 的数据。

可以使用三种建议的方法将 Application Insights 数据导出到 Power BI。 可以单独使用其中一种方法，或者结合使用这些方法。

* [**Power BI 适配器**](#power-pi-adapter)。 通过应用设置完整的遥测仪表板。 图表集是预定义的，但可以从其他源任何添加自己的查询。
* [**导出 Analytics 查询**](#export-analytics-queries)。 编写任何所需的查询，并将其导出到 Power BI。 可以使用 Analytics 或者通过使用情况漏斗图编写查询。 可将此查询连同其他所有数据一起放置在仪表板上。
* [**连续导出和 Azure 流分析**](app-insights-export-stream-analytics.md)。 如果想要长时间保留数据，此方法非常有用。 否则，请使用其他方法，因为此方法需要完成更多的设置工作。

## <a name="power-bi-adapter"></a>Power BI 适配器
此方法可以自动创建完整的遥测仪表板。 初始数据集是预定义的，但可以在其中添加更多数据。

### <a name="get-the-adapter"></a>获取适配器
1. 登录到 [Power BI](https://app.powerbi.com/)。
2. 打开“获取数据”、“服务”、“Application Insights”。
   
    ![从 Application Insights 数据源获取数据的屏幕截图](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. 提供 Application Insights 资源的详细信息。
   
    ![从 Application Insights 数据源获取数据的屏幕截图](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. 等待一两分钟来导入数据。
   
    ![Power BI 适配器的屏幕截图](./media/app-insights-export-power-bi/010.png)

可以编辑仪表板，将 Application Insights 图表与其他源的图表以及 Analytics 查询合并在一起。 可以在可视化库获取更多图表，其中每个图表都包含可以设置的参数。

完成初始导入后，仪表板和报告会持续每日更新。 可以控制数据集的刷新计划。

## <a name="export-analytics-queries"></a>导出 Analytics 查询
可以使用这种方法编写所需的任何 Analytics 查询或从使用情况漏斗图导出，然后将其导出到 Power BI 仪表板。 （可以添加到适配器创建的仪表板。）

### <a name="one-time-install-power-bi-desktop"></a>一次性操作：安装 Power BI Desktop
若要导入 Application Insights 查询，可以使用 Power BI Desktop（桌面版）。 随后可以将其发布到 Web 或 Power BI 云工作区。 

安装 [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)。

### <a name="export-an-analytics-query"></a>导出 Analytics 查询
1. [打开 Analytics 并编写查询](app-insights-analytics-tour.md)。
2. 测试并优化查询，直到对结果满意。 导出之前，请确保查询在 Analytics 中正常运行。
3. 在“导出”菜单中，选择“Power BI (M)”。 保存文本文件。
   
    ![Analytics 屏幕截图，其中突出显示了“导出”菜单](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. 在 Power BI Desktop 中，选择“获取数据” > “空白查询”。 然后，在查询编辑器中的“视图”下面，选择“高级查询编辑器”。

    将导出的 M 语言脚本粘贴到高级编辑器中。

    ![Power BI Desktop 的屏幕截图，其中突出显示了“高级编辑器”](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. 可能需要提供凭据才能让 Power BI 访问 Azure。 使用“组织帐户”和 Microsoft 帐户登录。
   
    ![Power BI“查询设置”对话框的屏幕截图](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    如果需要验证凭据，请使用查询编辑器中的“数据源设置”菜单命令。 请务必指定用于 Azure 的凭据，它可能不同于用于 Power BI 的凭据。
2. 选择查询的可视化效果并选择 X 轴、Y 轴和分段维度的字段。
   
    ![Power BI Desktop 可视化选项的屏幕截图](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. 将报告发布到 Power BI 云工作区。 在该工作区中，可将同步的版本嵌入其他网页。
   
    ![Power BI Desktop 的屏幕截图，其中突出显示了“发布”按钮](./media/app-insights-export-power-bi/publish-power-bi.png)
4. 定期手动刷新报告，或者在选项页中设置按计划刷新。

### <a name="export-a-funnel"></a>导出漏斗图
1. [生成漏斗图](usage-funnels.md)。
2. 选择“Power BI”。 

   ![Power BI 按钮的屏幕截图](./media/app-insights-export-power-bi/button.png)
   
3. 在 Power BI Desktop 中，选择“获取数据” > “空白查询”。 然后，在查询编辑器中的“视图”下面，选择“高级查询编辑器”。

   ![Power BI Desktop 的屏幕截图，其中突出显示了“空白查询”按钮](./media/app-insights-export-power-bi/blankquery.png)

   将导出的 M 语言脚本粘贴到高级编辑器中。 

   ![Power BI Desktop 的屏幕截图，其中突出显示了“高级编辑器”](./media/app-insights-export-power-bi/advancedquery.png)

4. 从查询中选择项并选择“漏斗图可视化效果”。

   ![Power BI Desktop 可视化选项的屏幕截图](./media/app-insights-export-power-bi/selectsequence.png)

5. 更改标题使其有意义，并将报表发布到 Power BI 云工作区。 

   ![Power BI Desktop 的屏幕截图，其中突出显示了标题更改](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>故障排除

你可能会遇到与凭据或数据集大小相关的错误。 下面是有关如何解决这些错误的一些信息。

### <a name="unauthorized-401-or-403"></a>未授权（401 或 403）
若未更新过刷新令牌，可能会出现此问题。 请尝试以下步骤，确保仍拥有访问权限：

1. 登录到 Azure 门户，确保可访问资源。
2. 尝试刷新仪表板的凭据。

 如果具有访问权限且刷新凭据不起作用，请开具支持票证。

### <a name="bad-gateway-502"></a>错误的网关 (502)
这通常由返回过多数据的分析查询导致。 尝试使用较小的时间范围执行查询。 

如果减少来自分析查询的数据集不符合需求，应考虑使用 [API](https://dev.applicationinsights.io/documentation/overview) 来拉取更大的数据集。 下面介绍如何转换 M-Query 导出以使用 API。

1. 创建 [API 密钥](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)。
2. 通过将 Azure 资源管理器 URL 替换为 Application Insights API，更新从分析中导出的 Power BI M 脚本。
   * 将 **https://management.azure.com/subscriptions/...** 替换
   * 为 **https://api.applicationinsights.io/beta/apps/...**
3. 最后，将凭据更新为基本凭据，再使用 API 密钥。
  

**现有脚本**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**更新的脚本**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>关于采样
如果应用程序发送大量数据，可以使用自适应采样功能，它只发送一定百分比的遥测数据。 如果在 SDK 中或者针对引入手动设置了采样，也同样可以做到这一点。 [了解有关采样的详细信息](app-insights-sampling.md)。


## <a name="next-steps"></a>后续步骤
* [Power BI - 学习](http://www.powerbi.com/learning/)
* [Analytics 教程](app-insights-analytics-tour.md)

