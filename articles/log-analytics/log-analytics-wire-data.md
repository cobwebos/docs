---
title: "Log Analytics 中的线路数据解决方案| Microsoft Docs"
description: "线路数据是通过 OMS 代理（包括 Operations Manager 和 Windows 连接的代理）从计算机收集的网络和性能整合数据。 网络数据与日志数据结合在一起，可帮助你将数据相关联。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: be00cb9b1e8ba5d9d8368695ca8d448d466e8f47


---
# <a name="wire-data-solution-in-log-analytics"></a>Log Analytics 中的线路数据解决方案
线路数据是通过 OMS 代理（包括 Operations Manager 和 Windows 连接的代理）从计算机收集的网络和性能整合数据。 网络数据与日志数据结合在一起，可帮助你将数据相关联。 在 IT 基础结构中的计算机上安装的 OMS 代理将监视发往这些计算机以及从这些计算机（网络级别为 2-3，处于 [OSI 模式](https://en.wikipedia.org/wiki/OSI_model)，包括使用的各种协议和端口）接收的网络数据。

> [!NOTE]
> 目前还不能将线路数据解决方案添加到工作区。 已启用线路数据解决方案的客户可以继续使用该解决方案。
>
>

默认情况下，OMS 从 Windows 中内置的计数器收集针对 CPU、内存和磁盘记录的数据以及网络性能数据。 网络以及其他数据的收集针对每个代理实时执行，包括正在由计算机使用的子网和应用程序级协议。 可以在“日志”选项卡的“设置”页面添加其他性能计数器。

如果曾经使用过 [sFlow](http://www.sflow.org/) 或其他包含 [Cisco 的 NetFlow 协议](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)的软件，则对于将要看到的来自线路数据的统计信息和数据你不会感到很陌生。

一些内置的日志搜索查询类型包括：

* 提供线路数据的代理
* 提供线路数据的代理 IP 地址
* 通过 IP 地址的出站通信
* 通过应用程序协议发送的字节数
* 通过应用程序服务发送的字节数
* 通过不同协议接收的字节数
* 通过 IP 发送和接收的总字节数
* 在 10.0.0.0/8 子网上与代理进行通信的 IP 地址
* 经可靠测量的平均连接延迟
* 启动或接收网络流量的计算机进程
* 某个进程的网络流量数

在使用线路数据搜索时，可以筛选数据并将其分组，以查看有关最突出的代理和协议的信息。 也可以深入了解某些计算机（IP 地址/MAC 地址）彼此之间何时进行通信、通信持续多长时间，以及发送了多少数据。基本上，你可以基于搜索查看有关网络流量的元数据。

但是，因为你正在查看元数据，因此这不一定适用于深入的故障排除。 OMS 中的线路数据并非完整捕获的网络数据。 所以不能用来进行数据包级别的深度故障排除。
与其他收集方法相比，使用代理的优点是不需要安装设备、重新配置您的网络交换机或执行复杂的配置。 线路数据完全基于代理 - 将代理安装在一台计算机上，它就会监视自己的网络流量。 另一个优点体现在当你想要监视在云提供商、主机托管服务提供商或 Microsoft Azure 中运行的工作负荷时。在这种情况下，用户本身不必拥有结构层。

与之相反，如果不在网络基础结构中的所有计算机上安装代理，你将不能全面了解在网络上所发生的情况。

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息来安装和配置解决方案。

* 线路数据解决方案从运行 Windows Server 2012 R2、Windows 8.1 和更高版本操作系统的计算机获取数据。
* 你想要获取线路数据的计算机上需要安装 Microsoft.NET Framework 4.0 或更高版本。
* 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的流程，将线路数据解决方案添加到 OMS 工作区。  无需进一步的配置。
* 如果想要查看特定解决方案的线路数据，你需要先将解决方案添加到 OMS 工作区。

## <a name="wire-data-data-collection-details"></a>线路数据解决方案的数据收集详细信息
线路数据使用已经启用的代理来收集网络流量的元数据。

下表显示了数据收集方法以及有关如何为线路数据收集数据的其他详细信息。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows（2012 R2 / 8.1 或更高版本） |![是](./media/log-analytics-wire-data/oms-bullet-green.png) |![是](./media/log-analytics-wire-data/oms-bullet-green.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |![否](./media/log-analytics-wire-data/oms-bullet-red.png) |每隔 1 分钟 |

## <a name="combining-wire-data-with-other-solution-data"></a>将线路数据与其他解决方案的数据相结合
以上所示的从内置查询返回的数据本身可能很有意思。 但是，只有在将线路数据与来自其他 OMS 解决方案的信息结合起来才能实现线路数据的作用。 例如，可以使用由安全和审核解决方案收集的安全事件数据，将其与线路数据结合，以查找命名进程的异常网络登录尝试。  在此示例中，你将使用 IN 和 DISTINCT 运算符联接搜索查询中的数据点。

要求：若要使用下面的示例，你需要安装安全和审核解决方案。 但是，可以使用来自其他解决方案的数据来与线路数据结合，以获得类似的结果。

### <a name="to-combine-wire-data-with-security-events"></a>将线路数据和安全事件相结合
1. 在“概述”页上单击“**WireData**”磁贴。
2. 在“**常见 WireData 查询**”列表中，单击“**进程的网络流量数(以字节为单位)**”以查看返回进程的列表。
    ![WireData 查询](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. 如果进程列表太长不方便查看，可以将搜索查询修改为类似以下格式：

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    下例中所示的是一个命名为 DancingPigs.exe 的进程，该进程可能显得有些可疑。
    ![WireData 搜索结果](./media/log-analytics-wire-data/oms-wiredata-02.png)
4. 使用列表中返回的数据，单击一个命名的进程。 在此示例中，单击的是 DancingPigs.exe。 如下所示的结果描述了网络流量的类型，例如通过不同协议执行的出站通信。
    ![显示已命名进程的 WireData 结果](./media/log-analytics-wire-data/oms-wiredata-03.png)
5. 因为安装了安全和审核解决方案，你可以通过使用 IN 和 DISTINCT 搜索查询运算符来修改搜索查询，以查明具有相同 ProcessName 字段值的安全事件。 当线路数据和其他解决方案日志具有相同格式的值时，就可以执行该操作了。 将搜索查询修改为类似以下格式：

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![显示已组合数据的 WireData 结果](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. 在上述结果中，你将看到显示的帐户信息。 现在你可以优化搜索查询，找出显示安全和审核数据的帐户被进程使用的频率，查询格式类似于：        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![显示帐户数据的 WireData 结果](./media/log-analytics-wire-data/oms-wiredata-05.png)

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的线路数据搜索记录。
* 请参阅 Dan 写的博客帖子 [Using Wire Data in Operations Management Suite Log Search](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx)（在 Operations Management Suite 日中搜索中使用线路数据）。该帖子补充说明了收集数据的频率以及如何为 Operations Manager 代理修改收集属性。



<!--HONumber=Nov16_HO3-->


