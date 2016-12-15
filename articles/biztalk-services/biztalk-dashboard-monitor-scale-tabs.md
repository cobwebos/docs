---
title: "BizTalk 服务中的“仪表板”、“监视”、“缩放”、“配置”和“混合连接” | Microsoft Docs"
description: "了解 BizTalk 服务经典门户选项卡上的控件和监视性能：“仪表板”、“监视”、“缩放”、“配置”和“混合连接”。 MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 27fb77bb2a6aadc2ab65f3916eeb68f487ae5fad


---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>查看“仪表板”、“监视”、“缩放”、“配置”和“混合连接”选项卡
创建 BizTalk 服务和部署应用程序后，可以更改某些 BizTalk 服务设置和监视应用程序性能。 

打开 Azure 经典门户时，系统会自动为你打开“所有项目”选项卡。 若要查看 BizTalk 服务，请在“所有项目”选项卡上选择你的 BizTalk 服务，或者选择“BIZTALK 服务”选项卡，然后选择你的 BizTalk 服务名称。

此时会打开一个新窗口，其中包含以下选项卡： 本主题介绍这些选项卡。

## <a name="quick-start-quick-startquickstart"></a>快速启动（![快速启动][QuickStart])
你不一定能够使用列出的所有选项，这取决于所用的 BizTalk 服务版本。 

<table border="1">
    <tr>
        <td><strong>获取工具</strong></td>
        <td>下载 BizTalk 服务 SDK，以便在本地开发计算机上安装 Visual Studio 项目模板。 这些模板可创建 <strong>BizTalk 服务</strong>（桥接）和 <strong>BizTalk 服务项目</strong>（转换）Visual Studio 项目，而这些项目将部署到你的 BizTalk 服务中。
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">如何开始使用 Azure BizTalk 服务 SDK</a> 和<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">安装 Azure BizTalk 服务 SDK</a> 列出了入门步骤。
        </td>
    </tr>
    <tr>
        <td><strong>创建合作伙伴协议</strong></td>
        <td>打开在 Azure 上托管的 Azure BizTalk 服务门户，你可在其中添加合作伙伴以及创建 X12、AS2 和 EDIFACT EDI 协议。
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服务门户上配置 EDI 消息传递的组件</a>列出了入门步骤。
        </td>
    </tr>

<tr>
        <td><strong>详细了解 BizTalk 服务</strong></td>
        <td>转到<a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">学习中心</a>以了解有关 Azure BizTalk 服务的更多信息。</td>
</tr>
</table>


在底部的任务栏中，你可以：

<table border="1">

<tr>
<td><strong>管理</strong>应用程序部署</td>
<td>打开 Azure BizTalk 服务门户。 BizTalk 服务门户是进行 EDI 配置的入口，包括添加合作伙伴和创建 X12、AS2 和 EDIFACT 协议。
<br/><br/>
这与“快速启动”<strong></strong>选项卡上的“创建合作伙伴协议”<strong></strong>相同。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服务门户上配置 EDI 消息传送的组件</a>提供了有关 BizTalk 服务门户的详细信息。</td>
</tr>

<tr>
<td><strong>访问控制命名空间的连接信息</strong></td>
<td>选择“连接信息”时，将显示“访问控制命名空间”、“默认颁发者”和“默认密钥”。 你可以复制这些值。
<br/><br/>
还可打开访问控制门户。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">创建访问控制命名空间</a>提供与访问控制门户有关的详细信息。</td>
</tr>

<tr>
<td><strong>存储帐户中的密钥同步</strong></td>
<td>当你创建存储帐户时，将自动创建主密钥和辅助密钥。 这些加密密钥可控制对存储帐户的访问。 BizTalk 服务自动使用主密钥。 “同步密钥”<strong></strong>使用户可以在主密钥与辅助密钥之间切换，而不会中断 BizTalk 服务。
<br/><br/>
例如，你希望 BizTalk 服务为存储帐户使用新的主密钥。 为此，请按以下步骤操作：
<br/><br/>
<ol>
<li>依次选择你的 BizTalk 服务和“同步密钥”<strong></strong>。 选择“辅助密钥”。 当你执行此操作时，BizTalk 服务开始使用辅助密钥。</li>
<li>在 Azure 经典门户中，选择“存储帐户”和“重新生成主密钥”。 请记住，你的 BizTalk 服务正在使用辅助密钥。</li>
<li>依次选择你的 BizTalk 服务和“同步密钥”<strong></strong>。 现在，选择“主密钥”。 这是你重新生成的新主密钥。</li>
<li>在 Azure 经典门户中，选择“存储帐户”和“重新生成辅助密钥”。</li>
</ol>
<br/>
此过程称为“变换密钥”。 其目的是让用户可以在主密钥与辅助密钥之间切换，而不会中断 BizTalk 服务。</td>
</tr>

<tr>
<td><strong>删除</strong>应用程序</td>
<td>当你选择“删除”时，将删除你的 BizTalk 服务和部署到此服务的所有项目。</td>
</tr>
</table>


## <a name="dashboard"></a>仪表板
你不一定能够使用列出的所有选项，这取决于所用的 BizTalk 服务版本。 

当你选择 BizTalk 服务名称时，将显示“仪表板”选项卡。 在仪表板中，可以：

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>使用情况概述：显示使用的混合连接数
另外还显示数据用量（以 GB 为单位）。 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>度量值图表：显示性能度量值的固定列表
这些度量值提供有关 BizTalk 服务的运行状况的实时值。 你还可以为图表中显示的度量值选择“相对”或“绝对”值，以及时间范围“间隔”。 

有关这些性能度量值的说明，请转到本主题中的[可用度量值](#Metrics)。

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>速览：列出 BizTalk 服务属性
<table border="1">

<tr>
<td><strong>更新跟踪数据库凭据</strong></td>
<td>更改用于登录到跟踪数据库的用户名和密码。</td>
</tr>
<tr>
<td><strong>更新 SSL 证书</strong></td>
<td>可以更新 BizTalk 服务以使用不同的 SSL 证书。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">创建 BizTalk 服务时</a>，系统将自动创建一个自签名 SSL 证书。</td>
</tr>
<tr>
<td><strong>下载证书</strong></td>
<td>可以将 BizTalk 服务使用的 SSL 证书下载到本地计算机。</td>
</tr>
<tr>
<td><strong>状态</strong></td>
<td>显示 BizTalk 服务的当前状态。 请参阅 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk 服务：服务状态图表</a>。 </td>
</tr>
<tr>
<td><strong>服务 URL</strong></td>
<td>BizTalk 服务的 URL。 这与你在创建 BizTalk 服务时输入的“域 URL”<strong></strong>相同。</td>
</tr>
<tr>
<td><strong>公用虚拟 IP (VIP) 地址</strong></td>
<td>分配给 BizTalk 服务的 IP 地址。 它用于所有输入终结点，并且是出站流量的源地址。 只要创建了 BizTalk 服务，此 IP 地址就属于你的 BizTalk 服务。 如果你删除 BizTalk 服务，则会将该 IP 地址分配给其他 BizTalk 服务。</td>
</tr>
<tr>
<td><strong>ACS 命名空间</strong></td>
<td>通过 BizTalk 服务进行身份验证。</td>
</tr>
<tr>
<td><strong>版本</strong></td>
<td>列出你在创建 BizTalk 服务时输入的版本。</td>
</tr>
<tr>
<td><strong>位置</strong></td>
<td>显示托管 BizTalk 服务的地理区域。</td>
</tr>
<tr>
<td><strong>已创建</strong></td>
<td>显示创建 BizTalk 服务时的日期和时间。</td>
</tr>
<tr>
<td><strong>跟踪数据库</strong></td>
<td>存储你的 BizTalk 服务所使用的跟踪表的 Azure SQL 数据库名称。 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要求说明</a>提供有关跟踪数据库的详细信息。</td>
</tr>
<tr>
<td><strong>监视/存档存储</strong></td>
<td>存储 BizTalk 服务的监视输出的 Azure 存储帐户名称。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">要求说明</a>提供有关存储帐户的详细信息。</td>
</tr>
<tr>
<td><strong>订阅名称</strong></td>
<td>列出托管 BizTalk 服务的订阅。 订阅控制对 Azure 经典门户的访问。</td>
</tr>
<tr>
<td><strong>订阅 ID</strong></td>
<td>当创建订阅时，将自动生成一个订阅 ID。 当使用 REST API 时，你可能需要输入“订阅 ID”。</td>
</tr>
</table>

[BizTalk 服务：使用 Azure 经典门户进行设置](http://go.microsoft.com/fwlink/p/?LinkID=302280)列出了创建 BizTalk 服务的步骤。

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>任务栏中的“管理”、“连接信息”、“同步密钥”和“删除”：
<table border="1">

<tr>
<td><strong>管理</strong>应用程序部署</td>
<td>打开 Azure BizTalk 服务门户。 BizTalk 服务门户是进行 EDI 配置的入口，包括添加合作伙伴和创建 X12、AS2 和 EDIFACT 协议。
<br/><br/>
这与“快速启动”<strong></strong>选项卡上的“创建合作伙伴协议”<strong></strong>相同。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">在 BizTalk 服务门户上配置 EDI 消息传送的组件</a>提供了有关 BizTalk 服务门户的详细信息。</td>
</tr>
<tr>
<td><strong>访问控制命名空间的连接信息</strong></td>
<td>显示“Access Control 命名空间”、“默认颁发者”和“默认密钥”值；可以复制这些值。
<br/><br/>
还可打开访问控制门户。 此访问控制门户与左导航窗格中使用“Active Directory”选项相同。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">管理 ACS 命名空间</a>提供与访问控制门户有关的详细信息。</td>
</tr>
<tr>
<td><strong>存储帐户中的密钥同步</strong></td>
<td>当你创建存储帐户时，将自动创建主密钥和辅助密钥。 这些加密密钥可控制对存储帐户的访问。 BizTalk 服务自动使用主密钥。 “同步密钥”<strong></strong>使用户可以在主密钥与辅助密钥之间切换，而不会中断 BizTalk 服务。
<br/><br/>
例如，你希望 BizTalk 服务为存储帐户使用新的主密钥。 为此，请按以下步骤操作：
<br/><br/>
<ol>
<li>依次选择你的 BizTalk 服务和“同步密钥”<strong></strong>。 选择“辅助密钥”。 当你执行此操作时，BizTalk 服务开始使用辅助密钥。</li>
<li>在 Azure 经典门户中，选择“存储帐户”和“重新生成主密钥”。 请记住，你的 BizTalk 服务正在使用辅助密钥。</li>
<li>依次选择你的 BizTalk 服务和“同步密钥”<strong></strong>。 现在，选择“主密钥”。 这是你重新生成的新主密钥。</li>
<li>在 Azure 经典门户中，选择“存储帐户”和“重新生成辅助密钥”。</li>
</ol>
<br/>
此过程称为“变换密钥”。 其目的是让用户可以在主密钥与辅助密钥之间切换，而不会中断 BizTalk 服务。</td>
</tr>

<tr>
<td><strong>删除</strong>应用程序</td>
<td>删除你的 BizTalk 服务和部署到此服务的所有项目。</td>
</tr>
</table>


## <a name="monitor"></a>监视
本部分的内容不适用于免费版。

选择 BizTalk 服务名称时，将会出现“监视”选项卡，其中显示了以下信息：

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>度量值图表：显示选定的性能度量值
这些度量值提供有关 BizTalk 服务的运行状况的实时值。 你可以选择要显示哪些性能度量值。 最多可同时显示六个性能度量值。 

你还可以为显示的度量值选择“相对”或“绝对”值，以及时间范围“间隔”。 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>在图表中删除或显示度量值：
1. 选择“监视”选项卡。
2. 在任务栏中选择“添加度量值”：  
   ![选择“添加度量值”][AddMetrics]
3. 检查要显示的性能度量值。
4. 单击复选标记可返回到“监视”选项卡。
5. 选择度量值旁边的圆圈以便在图表中显示该度量值的值。  
   
    例如，“CPU 使用率”度量值为灰显；其输出不会显示在图表中：  
   ![CPU 使用率度量值为灰显][GrayedMetric]  
   
    选择灰显的圆圈可启用“CPU 使用率”度量值，以便在图表中显示其输出：  
   ![CPU 使用率度量值已启用][EnabledMetric]
6. 若要从显示图表和列表中删除度量值，请选择任务栏中的“删除度量值”。 若要将此度量值重新添回到列表，请在任务栏中选择“添加度量值”，检查此度量值，并选择复选标记以返回到“监视”选项卡。 选择灰显的圆圈可启用该度量值。

## <a name="a-namemetricsaavailable-metrics"></a><a name="Metrics"></a>可用度量值
提供以下性能计数器/度量值：

<table border="1">

<tr>
<td><strong>往返延迟</strong></td>
<td>显示 BizTalk 服务跨所有桥处理一条消息所花的平均时间，单位为毫秒 (ms)（从收到消息开始，到完全处理此消息为止）。 仅对已成功处理的消息计数。<br/><br/>
发生以下事件时，将创建时间戳：
<ul>
<li>消息进入网关</li>
<li>消息路由到目标</li>
<li>收到目标响应</li>
<li>目标确认响应发送到网关</li>
</ul>
<br/>
此度量值显示以下计算的结果：
<br/><br/>
[目标确认响应发送到网关] – [消息进入网关]</td>
</tr>
<tr>
<td><strong>源故障数</strong></td>
<td>显示 BizTalk 服务从源终结点拉取消息时失败的总消息数量。</td>
</tr>
<tr>
<td><strong>CPU 使用率</strong></td>
<td>列出所有角色实例的平均 %Processor Time。</td>
</tr>
<tr>
<td><strong>处理延迟</strong></td>
<td>显示 BizTalk 服务跨所有桥处理一条消息所花的平均时间，单位为毫秒 (ms)（花在目标上的时间除外）。 仅对已成功处理的消息计数。<br/><br/>
发生以下每个事件时，将创建时间戳：

<ul>
<li>消息进入网关</li>
<li>消息路由到目标</li>
<li>收到目标响应</li>
<li>目标确认响应发送到网关</li>
</ul>
<br/>此度量值显示以下计算的结果：<br/><br/>
[目标确认响应发送到网关] – [消息进入网关] – [收到目标响应] + [消息路由到目标]</td>
</tr>
<tr>
<td><strong>处理失败次数</strong></td>
<td>显示 BizTalk 服务在某个时间间隔内跨所有桥进行处理时失败的消息总数。</td>
</tr>
<tr>
<td><strong>发送的消息数</strong></td>
<td>显示 BizTalk 服务在某个时间间隔内跨所有桥发送的总消息数。 当从管道中发送的一条消息到达路由目标时，此度量值将加一。 此度量值并不指示成功处理消息。<br/><br/>
在请求-答复方案中，当路由目标向管道发回一个接收确认时，此度量值将加一。</td>
</tr>
<tr>
<td><strong>收到的消息数</strong></td>
<td>显示 BizTalk 服务在某个时间间隔内跨所有桥接收的总消息数。 当管道收到一条新消息时，此度量值将加一。</td>
</tr>
<tr>
<td><strong>正在处理的消息数</strong></td>
<td>显示 BizTalk 服务在某个时间间隔内当前处理的总消息数。</td>
</tr>
<tr>
<td><strong>已处理的消息数</strong></td>
<td>显示 BizTalk 服务在某个时间间隔内跨所有桥成功处理的总消息数。 当管道成功收到一条消息且该消息成功路由到目标时，此度量值将加一。</td>
</tr>
</table>


## <a name="scale"></a>缩放
在“缩放”选项卡上，你可以添加或减少 BizTalk 服务使用的单位数量。 默认情况下配置了一个单位。 可以添加额外的单位以扩展 BizTalk 服务。 如果你扩大规模，则你是要增加吞吐量。 资源量也增加，包括部署的桥数量、协议、LOB 连接和处理能力。 例如，可以将规模从 1 个单位扩展到 2 个单位。 在此情况下，你可以部署双倍的桥数、双倍的协议、双倍的 LOB 连接和双倍的处理能力。

某些 BizTalk 版本未提供“缩放”选项。 在此情况下，允许一个“单位”。 若要确定所用版本可以扩展多少个单位，请参阅 [BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)。

增大单位数可能影响定价。 如果增加单位数，选择“保存”将显示一条消息，告诉你结算是否受影响。 然后，你可以进行选择以继续操作。 当你增大单位数后，BizTalk 服务的状态将从“有效”变为“正在更新”。 在“正在更新”状态下，BizTalk 服务继续运行。

[BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)定义了“单位”。

## <a name="configure"></a>配置
不适用于“混合连接”。

将“备份状态”设置为“无”或“自动”。 如果设置为“无”，则不自动创建备份。 如果设置为“自动”，则需要配置备份位置、备份频率以及备份文件的保存时间长度。 

[BizTalk 服务：备份和还原](biztalk-backup-restore.md)提供了详细信息。 

## <a name="a-namehybridconnectionsahybrid-connections"></a><a name="HybridConnections"></a>混合连接
混合连接可将 Web 应用或 Azure 应用服务中的移动应用等 Azure 应用程序连接到使用静态 TCP 端口的本地资源，例如 SQL Server、MySQL、HTTP Web API 和大多数自定义 Web 服务。 可以在 Azure 经典门户中的 BizTalk 服务内管理混合连接。

若要在 Azure 应用服务中创建混合连接，请参阅[在 Azure 应用服务中使用混合连接访问本地资源](../app-service-web/web-sites-hybrid-connection-get-started.md)。

若要在 Azure BizTalk 服务中创建或管理混合连接，请参阅[混合连接](integration-hybrid-connection-overview.md)。

## <a name="next"></a>下一步
在熟悉不同的选项卡后，你可以了解有关 Azure BizTalk 服务功能的详细信息：

* [BizTalk 服务：限制](biztalk-throttling-thresholds.md)  
* [BizTalk 服务：颁发者名称和颁发者密钥](biztalk-issuer-name-issuer-key.md)  
* [BizTalk 服务：备份和还原](biztalk-backup-restore.md)

## <a name="see-also"></a>另请参阅
* [混合连接](integration-hybrid-connection-overview.md)  
* [BizTalk 服务：开发人员版、基本版、标准版和高级版图表](biztalk-editions-feature-chart.md)  
* [BizTalk 服务：使用 Azure 经典门户预配](biztalk-provision-services.md)  
* [BizTalk 服务：BizTalk 服务状态图表](biztalk-service-state-chart.md)  
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png




<!--HONumber=Nov16_HO3-->


