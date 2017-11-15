---
title: "使用 Grafana 监控 Azure 服务和应用程序 | Microsoft 文档"
description: "路由 Azure Monitor 和 Application Insights 数据，以便在 Grafana 中进行查看。"
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: 709a98f8bcdb75962f8e41de348ca7a41c677610
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>在 Grafana 中监控 Azure 服务
你现在还可以使用 [Azure Monitor 数据源插件](https://grafana.com/plugins/grafana-azure-monitor-datasource)从 [Grafana](https://grafana.com/) 监控 Azure 服务和应用程序。 此插件将收集通过 Application Insights SDK 收集的应用程序性能数据，以及 Azure Monitor 提供的基础结构数据。 随后，可以在 Grafana 仪表板上显示此数据。

此插件目前为预览版。

使用以下步骤从 Azure Marketplace 设置 Grafana 服务器，并从 Azure Monitor 和 Application Insights 为指标生成仪表板。

## <a name="set-up-a-grafana-instance"></a>设置 Grafana 实例
1. 转到 Azure Marketplace 并选取 Grafana Labs 的 Grafana。

2. 填写名称和详细信息。 创建新的资源组。 记录为 VM 用户名、VM 密码和 Grafana 服务器管理员密码选择的值。  

3. 选择 VM 大小和存储帐户。

4. 配置网络配置设置。

5. 接受使用条款之后，查看摘要，然后选择“创建”。

## <a name="log-in-to-grafana"></a>登录到 Grafana
1. 部署完成后，选择“转到资源组”。 你将看到新创建的资源列表。 

    ![Grafana 资源组对象](.\media\monitor-how-to-grafana\grafana1.png) 

    如果选择网络安全组（在本例中为 grafana nsg），你可以看到端口 3000 用于访问 Grafana 服务器。 

2. 返回资源列表，然后选择“公共 IP 地址”。 使用在此屏幕上找到的值，在浏览器中键入 *http://<IP address>:3000* 或 *<DNSName>:3000*。 你应看到刚生成的 Grafana 服务器登录页。
    
    ![Grafana 登录屏幕](.\media\monitor-how-to-grafana\grafana2.png) 

3. 使用用户名 admin 和之前创建的 Grafana 服务器管理员密码登录。 

## <a name="configure-data-source-plugin"></a>配置数据源插件

成功登录后，你应看到 Azure Monitor 数据源插件已包括在内。

![Grafana 将显示 Azure Monitor 插件](.\media\monitor-how-to-grafana\grafana3.png) 

1. 选择“添加数据源”，以配置 Azure Monitor 和 Application Insights。 
    
2. 为数据源选择名称，并从下拉列表中选择“Azure Monitor”作为数据源。
    
    
## <a name="create-a-service-principal"></a>创建服务主体 

Grafana 使用 Azure Active Directory 服务主体连接到 Azure Monitor API 并收集指标数据。 你必须创建一个服务主体，以管理对 Azure 资源的访问权限。

1. 请参阅[这些说明](../azure-resource-manager/resource-group-create-service-principal-portal.md)以创建服务主体。 复制并保存你的租户 ID、客户端 ID 和客户端密码。

2. 请参阅[将应用程序分配给角色](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role)，将读取者角色分配给 Azure Active Directory 应用程序。   

3. 如果使用 Application Insights，还可以包含Application Insights API 和应用程序 ID，以收集基于 Application Insights 的指标。 有关详细信息，请参阅[获取 API 密钥和应用程序 ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)。

4. 输入以上所有信息后，选择“保存”，Grafana 将测试此 API。 应看到类似于下面的消息：  

    ![Grafana 将显示 Azure Monitor 插件](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>生成 Grafana 仪表板

1. 转到“主页”并选择“新建仪表板”。

2. 在新仪表板中，选择“Graph”。 你可以尝试其他图表选项，但本文使用 Graph 作为示例。 

    ![Grafana 新仪表板](.\media\monitor-how-to-grafana\grafana5.png) 

3. 在仪表板上将显示一个空白图表。 

4. 单击面板标题，然后选择“编辑”，输入你想要在此图表中绘制的数据详细信息。
    
5. 选择所有正确的 VM 后，即可开始在仪表板中查看指标。 

下面是一个简单的包含两个图表的仪表板。 左图显示两个 VM 的 CPU 百分比。 右图显示 Azure 存储帐户中的事务（按事务 API 类型分解）。
    
![Grafana 两个图表示例](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>可选：创建仪表板播放列表

仪表板播放列表是 Grafana 众多有用功能中的其中一项。 你可以创建多个仪表板，将其添加到播放列表，并配置每个仪表板的显示间隔。 选择“播放”即可查看仪表板循环。 你可能想要在大型监控屏幕墙上显示这些仪表板，为你的小组提供“状态板”。 
    
![Grafana 播放列表示例](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>可选：在同一 Grafana 服务器上监视自定义指标

此外，你还可以安装 Telegraf 和 InfluxDB，以在同一 Grafana 实例中收集和绘制自定义和基于代理的指标。 你可以使用许多数据源插件将这些指标集中到一个仪表板中。 
    
你也可以重复使用此设置来包含 Prometheus 服务器中的指标。 在 Grafana 插件库中使用 Prometheus 数据源插件。
    
如何使用 Telegraf、InfluxDB、Prometheus 和 Docker 的优秀参考文章如下：
 - [如何在 Ubuntu 16.04 上使用 TICK Stack 监控系统指标](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [使用 Grafana、InfluxDB 和 Telegraf 监控 Docker 资源指标](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [用于 Docker 主机、容器和容器化服务的监控解决方案](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

包含 Azure Monitor 和 Application Insights 指标的完整 Grafana 仪表板的图像如下。
![Grafana 示例指标](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>清理资源

无论你是否在使用 VM，一旦开始运行都是需要付费的。 为了避免产生额外的费用，请对此文章中创建的资源组进行清理。 

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“Grafana”。 
2. 在资源组页上，单击“删除”，在文本框中键入“Grafana”，然后单击“删除”。

## <a name="next-steps"></a>后续步骤
* [Azure Monitor 指标概述](monitoring-overview-metrics.md)


