---
title: 使用强力 BI 可视化 Azure 认知搜索日志和指标
description: 使用强力 BI 可视化 Azure 认知搜索日志和指标
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650126"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>使用强力 BI 可视化 Azure 认知搜索日志和指标
[Azure 认知搜索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)允许您在 Azure 存储帐户中存储有关搜索服务的操作日志和服务指标。 本页提供有关如何通过 Power BI 模板应用可视化该信息的说明。 该应用程序提供有关搜索服务的详细见解，包括有关搜索、索引、操作和服务指标的信息。

您可以在[Power BI 应用市场](https://appsource.microsoft.com/marketplace/apps)找到 Power BI 模板应用**Azure 认知搜索：分析日志和指标**。

## <a name="how-to-get-started-with-the-app"></a>如何开始使用应用程序
1. 为搜索服务启用诊断日志记录：
    1. 创建或标识现有的[Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，您可以在其中存档日志
    1. 在 Azure 门户中导航到 Azure 认知搜索服务
    1. 在左列的"监视"部分下，选择 **"诊断设置"**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. 选择 **+ 添加诊断设置**
    1. 检查**存储帐户的存档**，提供存储帐户信息，并检查**操作日志****和所有指标**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. 选择 **"保存"**

1. 启用日志记录后，使用搜索服务开始生成日志和指标。 容器最多需要一个小时才能与这些日志一起显示在 Blob 存储中。 您将看到用于搜索流量**日志的见解日志操作日志**容器和用于指标的**见解指标-pt1m**容器。

1. 在[Power BI 应用市场](https://appsource.microsoft.com/marketplace/apps)中查找 Azure 认知搜索电源 BI 应用，并将其安装到新工作区或现有工作区中。 该应用程序称为**Azure 认知搜索：分析日志和指标**。

1. 安装应用后，从 Power BI 中的应用列表中选择应用。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. 选择 **"连接"** 以连接数据

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. 输入包含日志和指标的存储帐户的名称。 默认情况下，应用将查看最近 10 天的数据，但可以使用 **"天"** 参数更改此值。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. 选择 **"密钥**"作为身份验证方法并提供存储帐户密钥。 选择 **"专用**"作为隐私级别。 单击“登录”开始加载程序。

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. 等待刷新数据。 这可能需要一些时间，具体取决于您拥有的数据量。 您可以根据以下指标查看数据是否仍在刷新。

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. 数据刷新完成后，选择 Azure**认知搜索报表**以查看报表。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. 请确保在打开报表后刷新页面，以便该页随数据一起打开。

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>如何更改应用参数
如果要可视化来自其他存储帐户的数据或更改要查询的数据天数，请按照以下步骤更改 **"天"** 和 **"存储帐户"** 参数。

1. 导航到 Power BI 应用，查找 Azure 认知搜索应用，然后选择 **"编辑应用**"按钮以查看工作区。

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. 从"数据集"选项中选择 **"设置**"。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. 在"数据集"选项卡中，更改参数值并选择"**应用**"。 如果连接出现问题，请更新同一页上的数据源凭据。

1. 导航回工作区，然后从"数据集"选项中选择 **"立即刷新**"。

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. 打开报表以查看更新的数据。 您可能还需要刷新报表才能查看最新数据。

## <a name="troubleshooting"></a>疑难解答
如果您发现看不到数据，请按照以下故障排除步骤操作：

1. 打开报表并刷新页面，以确保查看最新数据。 报表中有一个选项用于刷新数据。 选择此选项可获取最新数据。

1. 确保您提供的存储帐户名称和访问密钥正确无误。 存储帐户名称应对应于与搜索服务日志一起配置的帐户。

1. 确认存储帐户包含容器**见解-日志-操作日志**和**见解指标-pt1m，** 每个容器都有数据。 日志和指标将位于几个文件夹层内。

1. 检查数据集是否仍在刷新。 刷新状态指示器显示在上面的步骤 8 中。 如果仍处于刷新状态，请等待刷新完成以打开并刷新报表。

## <a name="next-steps"></a>后续步骤
[了解有关 Azure 认知搜索的更多信息](https://docs.microsoft.com/azure/search/)

[什么是 Power BI？](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Power BI 服务中设计器的基本概念](https://docs.microsoft.com/power-bi/service-basic-concepts)