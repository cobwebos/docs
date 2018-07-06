---
title: Log Analytics 中的 VMware 监视解决方案 | Microsoft Docs
description: 了解 VMware 监视解决方案如何帮助管理日志和监视 ESXi 主机。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: a538b23e829e309c10e745beef4fc8512c3294de
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131421"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Log Analytics 中的 VMware 监视（预览版）解决方案

![VMware 符号](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> VMware 监视解决方案已弃用。  已安装该解决方案的客户可以继续使用它，但 VMware 监视无法添加到任何新的工作区。

Log Analytics 中的 VMware 监视解决方案是一个有助于创建针对大型 VMware 日志的集中式日志记录和监视方法的解决方案。 本文介绍如何使用该解决方案在单个位置对 ESXi 主机进行故障排除、捕获和管理。 使用该解决方案，可以看到在单个位置中看到所有 ESXi 主机的详细数据。 可以看到通过 ESXi 主机日志提供的 VM 和 ESXi 主机的重要事件计数、状态及趋势。 可以通过查看和搜索集中式 ESXi 主机日志进行故障排除。 而且，可以基于日志搜索查询创建警报。

解决方案使用 ESXi 主机的本机 syslog功能将数据推送到具有 OMS 代理的目标 VM。 但是，该解决方案不会将文件写入到目标 VM 内的 syslog 中。 OMS 代理打开端口 1514 并对它进行侦听。 当接收到数据后，OMS 代理将数据推送到 Log Analytics 中。

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

* 使用[添加管理解决方案](log-analytics-add-solutions.md#add-a-management-solution)中所述的进程将 VMware 监视解决方案添加到订阅。

#### <a name="supported-vmware-esxi-hosts"></a>受支持的 VMware ESXi 主机
vSphere ESXi 主机 5.5、6.0 和 6.5

#### <a name="prepare-a-linux-server"></a>准备一台 Linux 服务器
创建 Linux 操作系统系统 VM，以接收来自 ESXi 主机的所有系统日志数据。 [OMS Linux 代理](log-analytics-linux-agents.md)是所有 ESXi 主机 syslog 数据的集合点。 可以使用多个 ESXi 主机将日志转发到一台 Linux 服务器，如以下示例所示。  

   ![系统日志流](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>配置系统日志收集
1. 为 VSphere 设置 syslog 转发。 有关帮助设置 syslog 转发的详细信息，请参阅[在 ESXi 5.0 及更高版本上配置 syslog (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)。 转到“ESXi 主机配置” > “软件” > “高级设置” > “Syslog”。
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. 在“Syslog.global.logHost”字段中，添加 Linux 服务器和端口号 *1514*。 例如，`tcp://hostname:1514` 或 `tcp://123.456.789.101:1514`
3. 为 syslog 打开 ESXi 主机防火墙。 “ESXi 主机配置” > “软件” > “安全配置文件” > “防火墙”并打开“属性”。  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. 检查 vSphere 控制台以验证该 syslog 是否已正确设置。 在 ESXI 主机上确认端口 **1514** 已配置。
5. 在 Linux 服务器上下载并安装适用于 Linux 的 OMS 代理。 有关详细信息，请参阅 [适用于 Linux 的 OMS 代理的文档](https://github.com/Microsoft/OMS-Agent-for-Linux)。
6. 安装 OMS Agent for Linux 后，转到 /etc/opt/microsoft/omsagent/sysconf/omsagent.d 目录，将 vmware_esxi.conf 文件复制到 /etc/opt/microsoft/omsagent/conf/omsagent.d 目录并更改文件的所有者/组和权限。 例如：

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. 通过运行 `sudo /opt/microsoft/omsagent/bin/service_control restart` 重新启动 OMS Agent for Linux。
8. 通过使用 ESXi 主机上的 `nc` 命令测试 Linux 服务器和 ESXi 主机之间的连接。 例如：

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. 在 Azure 门户中，针对 `VMware_CL` 执行日志搜索。 Log Analytics 收集 syslog 数据时，它将保留 syslog 格式。 在该门户中，将捕获某些特定字段，如 *Hostname* 和 *ProcessName*。  

    ![type](./media/log-analytics-vmware/type.png)  

    如果查看的日志搜索结果类似于上面的图像，则要设置使用 VMware 监视解决方案仪表板。  

## <a name="vmware-data-collection-details"></a>VMware 数据收集详细信息
VMware 监视解决方案使用已启用的 OMS Agent for Linux 从 ESXi 主机收集各种性能指标和日志数据。

下表显示数据收集方法以及有关如何收集数据的其他详细信息。

| 平台 | OMS Agent for Linux | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |每隔 3 分钟 |

下表显示了由 VMware 监视解决方案所收集的数据字段示例：

| 字段名称 | 说明 |
| --- | --- |
| Device_s |VMware 存储设备 |
| ESXIFailure_s |失败类型 |
| EventTime_t |事件发生的时间 |
| HostName_s |ESXi 主机名 |
| Operation_s |创建 VM 或删除 VM |
| ProcessName_s |事件名称 |
| ResourceId_s |VMware 主机的名称 |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceName_s |Hyper-V |
| SCSIStatus_s |VMware SCSI 状态 |
| SyslogMessage_s |Syslog 数据 |
| UserName_s |创建或删除 VM 的用户 |
| VMName_s |VM 名称 |
| Computer |主机 |
| TimeGenerated |生成数据的时间 |
| DataCenter_s |VMware 数据中心 |
| StorageLatency_s |存储延迟（毫秒） |

## <a name="vmware-monitoring-solution-overview"></a>VMware 监视解决方案概述
VMware 磁贴显示在 Log Analytics 工作区中。 它提供任何失败的高级视图。 单击该磁贴时，将进入仪表板视图。

![磁贴](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>导航仪表板视图
在“VMware”仪表板视图中，边栏选项卡的组织方式如下：

* 失败状态计数
* 按事件计数排名靠前的主机
* 排名靠前的事件计数
* 虚拟机活动
* ESXi 主机磁盘事件

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

单击任意边栏选项卡，打开 Log Analytics 搜索窗格，其中显示了特定于边栏选项卡的详细信息。

在此处可以编辑搜索查询，以对某些具体内容进行修改。 有关创建日志搜索的详细信息，请参阅[在 Log Analytics 中使用日志搜索查找数据](log-analytics-log-searches.md)。

#### <a name="find-esxi-host-events"></a>查找 ESXi 主机事件
单台 ESXi 主机基于其进程会生成多个日志。 VMware 监视解决方案可对其进行集中管理，并总结事件计数。 此集中式视图可帮助你了解哪些 ESXi 主机有大量的事件，以及在环境中最常发生哪些事件。

![event](./media/log-analytics-vmware/events.png)

可以通过单击 ESXi 主机或事件类型进一步了解相关信息。

单击 ESXi 主机名时，可查看该 ESXi 主机的信息。 如果要缩小包含事件类型的结果范围，将 `“ProcessName_s=EVENT TYPE”` 添加到搜索查询中。 可以在搜索筛选器中选择“ProcessName”。 会缩小信息范围。

![钻取](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>查找排名靠前的 VM 活动
可在任何 ESXi 主机上创建和删除虚拟机。 它可以帮助管理员识别 ESXi 主机创建的 VM 数量。 反过来，可帮助了解性能和容量规划。 管理环境时，跟踪 VM 活动事件至关重要。

![钻取](./media/log-analytics-vmware/vmactivities1.png)

如果要查看其他 ESXi 主机 VM 创建数据，请单击 ESXi 主机名。

![钻取](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>常用的搜索查询
该解决方案包括可帮助你管理 ESXi 主机的其他有用查询，如高存储空间、存储延迟和路径故障。

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![查询](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>保存查询
保存搜索查询是 Log Analytics 中的标准功能，可帮助你记录发现的任何有用查询。 创建有用的查询后，单击“收藏夹”将其保存下来。 已保存的查询可在以后从[我的仪表板](log-analytics-dashboards.md)页（可在其中创建你自己的自定义仪表板）轻松地对其进行重复使用。

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>从查询中创建警报
创建查询后，你可能想要使用查询在特定事件发生时向你发出警报。 有关如何创建警报的信息，请参阅 [Log Analytics 中的警报](log-analytics-alerts.md)。 有关警报查询示例和其他查询示例，请参阅[使用 Log Analytics 监视 VMware](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) 博客文章。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>需要对 ESXi 主机设置执行哪些操作？ 这对目前的环境有什么影响？
解决方案使用转发机制的本机 ESXi 主机 Syslog。 无需在 ESXi 主机上安装任何其他的 Microsoft 软件，便可捕获日志。 它对现有环境应该不会造成大的影响。 但是，需要设置 syslog 转发这一 ESXI 功能。

### <a name="do-i-need-to-restart-my-esxi-host"></a>是否需要重启 ESXi 主机？
不是。 此执行过程不需要重启主机。 有时，vSphere 不会正确更新 syslog。 这种情况下，请登录到 ESXi 主机并重新加载 syslog。 同样，无需重启主机，使该过程不会造成环境中断。

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>是否可以增减发送到 Log Analytics 的日志数据量？
可以。 可以在 vSphere 中使用 ESXi 主机日志级别设置。 日志集合基于 *info* 级别。 因此，如果想要审核 VM 创建或删除，则需在 Hostd 上保持 *info* 级别。 有关详细信息，请参阅 [VMware 知识库](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658)。

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>为什么 Hostd 不向 Log Analytics 提供数据？ 日志设置已设置为 info。
syslog 时间戳有一个 ESXi 主机 bug。 有关详细信息，请参阅 [VMware 知识库](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202)。 采用解决方法处理后，Hostd 应能够正常工作。

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>是否可以拥有多个 ESXi 主机将 syslog 数据转发到具有 omsagent 的单个 VM？
是的。 可以拥有多个 ESXi 主机将数据转发到具有 omsagent 的单个 VM。

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>为什么看不到数据流入 Log Analytics？
可能有多个原因造成此情况：

* ESXi 主机并未正确地将数据推送到运行 omsagent 的 VM。 若要对此进行测试，请执行以下步骤：

  1. 若要确认，请登录到使用 ssh 的 ESXi 主机，并运行以下命令：`nc -z ipaddressofVM 1514`

      如果此操作不成功，则高级配置中的 vSphere 设置可能不正确。 若要深入了解如何设置 ESXi 主机以进行 syslog 转发，请参阅[配置 syslog 集合](#configure-syslog-collection)。
  2. 如果 syslog 端口连接成功，但仍看不到任何数据，则通过使用 ssh 运行以下命令在 ESXi 主机上重新加载 syslog：` esxcli system syslog reload`
* 具有 OMS 代理的 VM 设置不正确。 若要对此进行测试，请执行以下步骤：

  1. Log Analytics 侦听端口 1514。 若要验证其是否打开，请运行以下命令：`netstat -a | grep 1514`
  2. 应看到端口 `1514/tcp` 处于打开状态。 如果未看到，请验证 omsagent 是否正确安装。 如果看不到端口信息，则未在 VM 上打开 syslog 端口。

    a. 使用 `ps -ef | grep oms` 验证 OMS 代理是否正在运行。 如果未运行，通过运行 ` sudo /opt/microsoft/omsagent/bin/service_control start` 命令启动此进程

    b. 打开 `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` 文件。

    c. 验证适当的用户和组设置是否有效，类似于：`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. 如果文件不存在或用户和组设置有误，则通过[准备 Linux 服务器](#prepare-a-linux-server)采取纠正措施。

## <a name="next-steps"></a>后续步骤
* 使用 Log Analytics 中的“[日志搜索](log-analytics-log-searches.md)”可查看详细的 VMware 主机数据。
* [创建自己的仪表板](log-analytics-dashboards.md)显示了 VMware 主机数据。
* 发生特定 VMware 主机事件时[创建警报](log-analytics-alerts.md)。
