---
title: 使用 Grafana 监视 Azure 服务和应用程序
description: 路由 Azure Monitor 和 Application Insights 数据，以便在 Grafana 中进行查看。
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672202"
---
# <a name="monitor-your-azure-services-in-grafana"></a>在 Grafana 中监控 Azure 服务
你现在可以使用 [Azure Monitor 数据源插件](https://grafana.com/)从 [Grafana](https://grafana.com/plugins/grafana-azure-monitor-datasource) 监控 Azure 服务和应用程序。 该插件收集 Azure Monitor 所收集的应用程序性能数据，包括各种日志和指标。 随后，可以在 Grafana 仪表板上显示此数据。

使用以下步设置 Grafana 服务器，并通过 Azure Monitor 为指标和日志生成仪表板。

## <a name="set-up-a-grafana-server"></a>设置 Grafana 服务器

### <a name="set-up-grafana-locally"></a>本地设置 Grafana
若要设置本地的 Grafana 服务器，请[在本地环境下载并安装 Grafana](https://grafana.com/grafana/download)。 若要使用插件的 Azure Monitor 集成，请安装 Grafana 5.3 或更高版本。

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>通过 Azure 市场在 Azure 上设置 Grafana
1. 转到 Azure 市场并选取 Grafana Labs 的 Grafana。

2. 填写名称和详细信息。 创建新的资源组。 记录为 VM 用户名、VM 密码和 Grafana 服务器管理员密码选择的值。  

3. 选择 VM 大小和存储帐户。

4. 配置网络配置设置。

5. 接受使用条款之后，查看摘要，然后选择“创建”。

6. 部署完成后，选择“转到资源组”。 你将看到新创建的资源列表。

    ![Grafana 资源组对象](media/grafana-plugin/grafana1.png)

    如果选择网络安全组（在本例中为 grafana nsg），你可以看到端口 3000 用于访问 Grafana 服务器。

7. 获取 Grafana 服务器的公共 IP 地址 - 返回到资源列表，然后选择“公共 IP 地址”。

## <a name="sign-in-to-grafana"></a>登录到 Grafana

1. 使用服务器的 IP 地址在浏览器中打开登录页 http://*IP 地址\<:3000 或 \>DNSName>* 3000 *\<\:* 。 尽管默认端口为 3000，但请注意可能在设置期间选择了其他端口。 你应看到生成的 Grafana 服务器登录页。

    ![Grafana 登录屏幕](./media/grafana-plugin/grafana-login-screen.png)

2. 用用户名*admin*和前面创建的 Grafana 服务器管理员密码登录。 如果使用本地安装程序，默认密码将为 admin，并要求你在首次登陆时更改密码。

## <a name="configure-data-source-plugin"></a>配置数据源插件

成功登录后，你应看到 Azure Monitor 数据源插件已包括在内。

![Grafana 包括 Azure Monitor 插件](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. 选择“添加数据源”，以添加并配置 Azure Monitor 数据源。

2. 为数据源选择名称，并从下拉列表中选择“Azure Monitor”作为类型。

3. 创建服务主体 - Grafana 使用 Azure Active Directory 服务主体连接到 Azure Monitor API 并收集数据。 必须创建新的或使用现有的服务主体，以管理对 Azure 资源的访问权限。
    * 请参阅[这些说明](../../azure-resource-manager/resource-group-create-service-principal-portal.md)以创建服务主体。 复制并保存租户 ID（目录 ID）、客户端 ID（应用程序 ID）和客户端密码（应用程序密钥值）。
    * 请参阅[将应用程序分配到角色](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)，以向要监视的订阅、资源组或资源上的 Azure Active Directory 应用程序分配读者角色。 
    Log Analytics API 需要 [Log Analytics 读者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)，其中包括读者角色权限并向其添加。

4. 为要使用的 API 提供连接详细信息。 可以连接到所有或其中部分。 
    * 如果在 Azure Monitor 中同时连接到指标和日志，可以通过选择与**AZURE MONITOR API 相同的详细信息**来重复使用相同的凭据。
    * 配置插件时，可以指示插件要监控哪个 Azure 云（公共、Azure 美国政府、Azure 德国或 Azure 中国）。
    * 如果使用 Application Insights，还可以包含Application Insights API 和应用程序 ID，以收集基于 Application Insights 的指标。 有关详细信息，请参阅[获取 API 密钥和应用程序 ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)。

        > [!NOTE]
        > 某些数据源字段的命名方式不同于其关联的 Azure 设置：
        > * 租户 ID 是 Azure 目录 ID
        > * 客户端 ID 是 Azure Active Directory 应用程序 ID
        > * 客户端密钥是 Azure Active Directory 应用程序密钥值

5. 如果使用 Application Insights，还可以包含Application Insights API 和应用程序 ID，以收集基于 Application Insights 的指标。 有关详细信息，请参阅[获取 API 密钥和应用程序 ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)。

6. 选择“保存”，然后 Grafana 将为每个 API 测试凭据。 应看到类似于下面的消息：  
    ![已批准 Grafana 数据源配置](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>生成 Grafana 仪表板

1. 转到 Grafana 主页，并选择“新建仪表板”。

2. 在新仪表板中，选择“Graph”。 你可以尝试其他图表选项，但本文使用 Graph 作为示例。

3. 在仪表板上将显示一个空白图表。 单击面板标题，然后选择“编辑”，输入你想要在此图表中绘制的数据详细信息。
    ![Grafana 新图形](./media/grafana-plugin/grafana-new-graph-dark.png)

4. 选择已配置的 Azure Monitor 数据源。
   * 收集 Azure Monitor 指标 - 在服务下拉列表中选择“Azure Monitor”。 随即将显示选择器列表，可在其中选择此图表中要监视的资源和指标。 若要收集 VM 的指标，请使用命名空间 Microsoft.Compute/VirtualMachines。 选择 VM 和指标后，即可开始在仪表板中查看其数据。
     ![适用于 Azure Monitor 的 Grafana 图形配置](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * 收集 Azure Monitor 日志数据-选择 "服务" 下拉列表中的 " **Azure Log Analytics** "。 选择要查询的工作区并设置查询文本。 可以在此处复制已有的任何日志查询，也可以创建一个新查询。 在查询中键入时，IntelliSense 将显示并建议自动完成选项。 选择可视化效果类型、**时序** **表**，并运行查询。
    
     > [!NOTE]
     >
     > 插件提供的默认查询使用两个宏："$__timeFilter() 和 $__interval。 
     > 这些宏允许 Grafana 在你放大某部分图表时动态计算时间范围和时间粒度。 可以删除这些宏并使用标准时间筛选器（如 TimeGenerated > ago(1h)），但这意味着未来图形将不支持放大功能。
    
     ![适用于 Azure Log Analytics 的 Grafana 图形配置](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. 下面是一个简单的包含两个图表的仪表板。 左图显示两个 VM 的 CPU 百分比。 右图显示 Azure 存储帐户中的事务（按事务 API 类型分解）。
    ![Grafana 两个图表示例](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>可选：在同一 Grafana 服务器上监视自定义指标

此外，你还可以安装 Telegraf 和 InfluxDB，以在同一 Grafana 实例中收集和绘制自定义和基于代理的指标。 你可以使用许多数据源插件将这些指标集中到一个仪表板中。

你也可以重复使用此设置来包含 Prometheus 服务器中的指标。 在 Grafana 插件库中使用 Prometheus 数据源插件。

以下为如何使用 Telegraf、InfluxDB、Prometheus 和 Docker 的优秀参考文章：
 - [如何在 Ubuntu 16.04 上使用 TICK Stack 监控系统指标](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [用于 Docker 主机、容器和容器化服务的监视解决方案](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

包含 Azure Monitor 和 Application Insights 指标的完整 Grafana 仪表板的图像如下。
![Grafana 示例指标](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>高级 Grafana 功能

### <a name="variables"></a>变量
可以通过 UI 下拉列表选择某些查询值，并在查询中进行更新。 考虑将以下查询示例：
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

可以配置将列出所有可用的解决方案值的变量，然后更新查询以便使用它。
若要创建新的变量，请单击右上角的仪表板“设置”按钮，选择“变量”，然后单击“新建”。
在变量页上，定义要运行的数据源和查询以获取值列表。
![Grafana 配置变量](./media/grafana-plugin/grafana-configure-variable-dark.png)

创建后请调整查询以使用所选值，并且图表将做出相应响应：
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana 使用变量](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>创建仪表板播放列表

仪表板播放列表是 Grafana 众多有用功能中的其中一项。 你可以创建多个仪表板，将其添加到播放列表，并配置每个仪表板的显示间隔。 选择“播放”即可查看仪表板循环。 建议在大型壁式监视器上显示这些仪表板，为你的小组提供状态板。

![Grafana 播放列表示例](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>清理资源

如果已在 Azure 上设置 Grafana 环境，则无论是否在使用 VM，一旦 VM 开始运行你都需要付费。 为了避免产生额外的费用，请对此文章中创建的资源组进行清理。

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“Grafana”。
2. 在资源组页上，单击“删除”，在文本框中键入“Grafana”，然后单击“删除”。

## <a name="next-steps"></a>后续步骤
* [Azure Monitor 指标概述](data-platform.md)

