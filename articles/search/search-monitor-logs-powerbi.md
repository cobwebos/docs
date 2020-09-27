---
title: 通过 Power BI 直观显示 Azure 认知搜索日志和指标
description: 通过 Power BI 直观显示 Azure 认知搜索日志和指标
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 90691fda7b0bf58768c7e9be6a78fb27a7807186
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400360"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>通过 Power BI 直观显示 Azure 认知搜索日志和指标
[Azure 认知搜索](./search-what-is-azure-search.md) 允许你将有关搜索服务的操作日志和服务指标存储在 azure 存储帐户中。 本页提供有关如何通过 Power BI 模板应用来可视化该信息的说明。 该应用提供有关搜索服务的详细信息，包括有关搜索、索引、操作和服务指标的信息。

可在[Power BI 应用 marketplace](https://appsource.microsoft.com/marketplace/apps)中找到 Power BI 模板应用**Azure 认知搜索：分析日志和指标**。

## <a name="how-to-get-started-with-the-app"></a>如何开始应用应用

1. 为搜索服务启用指标和资源日志记录：

    1. 创建或标识可以将日志存档到其中的现有[Azure 存储帐户](../storage/common/storage-account-create.md)
    1. 导航到 Azure 门户中的 Azure 认知搜索服务
    1. 在左侧列中的 "监视" 部分下，选择 "**诊断设置**"

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="显示如何在 Azure 认知搜索服务的 "监视" 部分中选择诊断设置的屏幕截图。" border="false":::

    1. 选择“+ 添加诊断设置”
    1. 选中 "**存档到存储帐户**"，提供存储帐户信息，并选中 " **insight-logs-operationlogs** " 和 " **AllMetrics** "

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="显示如何在 "诊断设置" 页中进行指标和资源日志记录选择的屏幕截图。":::
    1. 选择“保存”

1. 启用日志记录后，使用搜索服务开始生成日志和指标。 在 Blob 存储中显示这些日志之前，最多需要一小时。 你将看到一个用于搜索流量日志的 **见解-insight-logs-operationlogs** 容器和一个用于指标的 **insights-pt1m** 容器。

1. 在 [Power BI 应用商店](https://appsource.microsoft.com/marketplace/apps) 中找到 Azure 认知搜索 Power BI 应用，并将其安装到新的工作区或现有工作区。 此应用称为 **Azure 认知搜索：分析日志和指标**。

1. 安装应用后，从 Power BI 中的应用列表中选择应用。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="显示要从应用列表中选择的 Azure 认知搜索应用的屏幕截图。":::

1. 选择 " **连接** " 以连接数据

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="显示如何连接到 Azure 认知搜索应用中的数据的屏幕截图。":::

1. 输入包含日志和指标的存储帐户的名称。 默认情况下，该应用将查看过去10天的数据，但可以使用 **days** 参数更改此值。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="显示如何在 "连接到 Azure 认知搜索" 页中输入存储帐户名称和要查询的天数的屏幕截图。":::

1. 选择 " **密钥** " 作为身份验证方法并提供存储帐户密钥。 选择 " **专用** " 作为 "隐私级别"。 单击“登录”开始加载程序。

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="显示如何在 "连接到 Azure 认知搜索" 页中输入身份验证方法、帐户密钥和隐私级别的屏幕截图。":::

1. 等待数据刷新。 这可能需要一些时间，具体取决于你拥有的数据量。 您可以查看是否仍根据下面的指示刷新数据。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="显示如何读取 "数据刷新" 页上的信息的屏幕截图。":::

1. 完成数据刷新后，选择 " **Azure 认知搜索报表** " 以查看报表。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="显示如何在 "数据刷新" 页上选择 Azure 认知搜索报表的屏幕截图。":::![]()

1. 请确保在打开报表后刷新页面，使其与数据一起打开。

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Azure 认知搜索 Power BI 报表的屏幕截图。":::

## <a name="how-to-change-the-app-parameters"></a>如何更改应用程序参数
如果要从不同的存储帐户中显示数据，或更改要查询的数据的天数，请按照以下步骤更改 **days** 和 **StorageAccount** 参数。

1. 导航到 Power BI 应用，找到 Azure 认知搜索应用，然后选择 " **编辑应用** " 按钮以查看工作区。

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="显示如何选择 Azure 认知搜索应用程序的 "编辑应用程序" 按钮的屏幕截图。":::

1. 从数据集选项中选择 **设置** 。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="显示如何从 Azure 认知搜索数据集选项中选择设置的屏幕截图。":::

1. 在 "数据集" 选项卡中，更改参数值，并选择 " **应用**"。 如果连接出现问题，请更新同一页上的数据源凭据。

1. 导航回工作区，并从数据集选项中选择 " **立即刷新** "。

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="显示如何从 Azure 认知搜索数据集选项中选择 "立即刷新" 的屏幕截图。":::

1. 打开报表以查看更新后的数据。 你可能还需要刷新报表以查看最新数据。

## <a name="troubleshooting"></a>疑难解答
如果发现你看不到你的数据，请执行以下故障排除步骤：

1. 打开报表并刷新页面，确保您查看的是最新数据。 报表中有一个用于刷新数据的选项。 选择此以获取最新数据。

1. 确保你提供的存储帐户名称和访问密钥是正确的。 存储帐户名称应对应于配置了搜索服务日志的帐户。

1. 确认存储帐户包含 container **insights-insight-logs-operationlogs** 和 **insights-pt1m** ，每个容器都包含数据。 日志和度量值将位于几层文件夹中。

1. 查看数据集是否仍在进行刷新。 上面的步骤8显示了刷新状态指示器。 如果仍在刷新，请等待刷新完成，以打开并刷新报表。

## <a name="next-steps"></a>后续步骤
[了解有关 Azure 认知搜索的详细信息](./index.yml)

[什么是 Power BI？](/power-bi/fundamentals/power-bi-overview)

[Power BI 服务中设计器的基本概念](/power-bi/service-basic-concepts)