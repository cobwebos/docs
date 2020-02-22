---
title: Azure HDInsight 中的 Apache Ambari 陈旧警报
description: HDInsight 中 Apache Ambari 陈旧警报的可能原因和解决方案的讨论和分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539104"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>方案： Azure HDInsight 中的 Apache Ambari 陈旧警报

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在 Apache Ambari UI 中，你可能会看到如下所示的警报：

![Apache Ambari 陈旧警报示例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

Ambari 代理持续监视多个资源的运行状况。 可以将*警报*配置为通知你特定群集属性是否在预先确定的阈值内。 每次运行资源检查后，如果满足警报条件，Ambari 代理会将状态报告回 Ambari 服务器，并触发警报。 如果警报未根据其警报配置文件中的间隔进行检查，则服务器将触发*Ambari Server 陈旧警报*警报。

运行状况检查可能无法在其定义的时间间隔内运行的原因有多种：

* 主机的使用量较高（CPU 使用率高），因此 Ambari 代理无法获得足够的系统资源来按时运行警报。

* 群集在繁重的负载期间正在忙于执行多个作业或服务。

* 群集中的少量主机承载许多组件，因此需要运行多个警报。 如果组件数量很大，警报作业可能会错过计划的时间间隔。

## <a name="resolution"></a>解决方法

请尝试以下方法来解决 Ambari 陈旧警报的问题。

### <a name="increase-the-alert-interval-time"></a>增加警报间隔时间

你可以根据群集的响应时间和负载增加单个警报间隔的值：

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 选择所需的警报定义名称。
1. 从定义中选择 "**编辑**"。
1. 增大**检查间隔**值，然后选择 "**保存**"。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 服务器警报的警报间隔时间

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 从 "**组**" 下拉列表中，选择 " **AMBARI 默认值**"。
1. 选择**Ambari 服务器警报**警报。
1. 从定义中选择 "**编辑**"。
1. 增大**检查间隔**值。
1. 增大 "**间隔乘数**" 值，然后选择 "**保存**"。

### <a name="disable-and-reenable-the-alert"></a>禁用并重新启用警报

若要放弃过期警报，请禁用然后重新启用它：

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 选择所需的警报定义名称。
1. 在该定义中，选择 UI 最右端的 "**启用**"。
1. 在**确认**弹出窗口中，选择 "**确认禁用**"。
1. 等待几秒钟，以便清除该页上显示的所有警报 "实例"。
1. 在该定义中，选择 UI 最右端的 "**禁用**"。
1. 在**确认**弹出窗口中，选择 "**确认启用**"。

### <a name="increase-the-alert-grace-period"></a>增加警报宽限期

在 Ambari 代理报告已配置的警报丢失其计划之前有一个宽限期。 如果警报丢失了计划时间，但在宽限期内运行，则不会生成过时警报。

默认 `alert_grace_period` 值为5秒。 可以在/etc/ambari-agent/conf/ambari-agent.ini. 中配置此设置。 对于定期发生陈旧警报的主机，请尝试将该值增加到10。 然后，重新启动 Ambari 代理。

## <a name="next-steps"></a>后续步骤

如果此处未提及你的问题，或者你无法解决问题，请访问以下某个渠道获取更多支持：

* 在 azure[社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接 Twitter 上的[@AzureSupport](https://twitter.com/azuresupport) 。 这是用于改善客户体验的官方 Microsoft Azure 帐户。 它将 Azure 社区连接到适当的资源：答案、支持和专家。

* 如果需要更多帮助，请从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 若要实现此功能，请从门户菜单中选择 "帮助（ **？** ）"，或打开 "**帮助 + 支持**" 窗格。 有关详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 

  Microsoft Azure 订阅中包含对订阅管理和计费的支持。 技术支持通过[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供。
