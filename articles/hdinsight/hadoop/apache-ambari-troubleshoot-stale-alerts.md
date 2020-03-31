---
title: Azure HDInsight 中的阿帕奇安巴里陈旧警报
description: 讨论和分析 HDInsight 中 Apache Ambari 陈旧警报的可能原因和解决方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539104"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Ambari 陈旧警报

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Apache Ambari UI 中，您可能会看到如下所示的警报：

![阿帕奇·安巴里陈旧警报示例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

安巴里特工持续监测许多资源的健康。 可以配置*警报*来通知您特定的群集属性是否在预定阈值内。 每次资源检查运行后，如果满足警报条件，Ambari 代理将状态报告回 Ambari 服务器并触发警报。 如果未根据其警报配置文件中的时间间隔检查警报，服务器将触发*Ambari 服务器过时警报*。

运行状况检查可能无法在其定义的时间间隔运行的原因有多种：

* 主机使用量很大（CPU 使用率高），因此 Ambari 代理无法获得足够的系统资源以按时运行警报。

* 群集正忙于在负载沉重的时期执行许多作业或服务。

* 群集中的少量主机承载许多组件，因此需要运行许多警报。 如果组件数很大，则警报作业可能会错过其计划的时间间隔。

## <a name="resolution"></a>解决方法

请尝试以下方法解决 Ambari 陈旧警报的问题。

### <a name="increase-the-alert-interval-time"></a>增加警报间隔时间

您可以根据群集的响应时间和负载增加单个警报间隔的值：

1. 在 Apache Ambari UI 中，选择 **"警报"** 选项卡。
1. 选择所需的警报定义名称。
1. 从定义中，选择 **"编辑**"。
1. 增加**检查间隔**值，然后选择 **"保存**"。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 服务器警报的警报间隔时间

1. 在 Apache Ambari UI 中，选择 **"警报"** 选项卡。
1. 从 **"组**"下拉列表中，选择**AMBARI 默认值**。
1. 选择**Ambari 服务器警报**。
1. 从定义中，选择 **"编辑**"。
1. 增加**检查间隔**值。
1. 增加**间隔乘数**值，然后选择 **"保存**"。

### <a name="disable-and-reenable-the-alert"></a>禁用并重新启用警报

要丢弃过期警报，请禁用并重新启用它：

1. 在 Apache Ambari UI 中，选择 **"警报"** 选项卡。
1. 选择所需的警报定义名称。
1. 从定义中选择 UI 最右侧的 **"已启用**"。
1. 在 **"确认**"弹出窗口中，选择 **"确认禁用**"。
1. 等待几秒钟，清除页面上显示的所有警报"实例"。
1. 从定义中选择 UI 最右侧的 **"禁用**"。
1. 在 **"确认**"弹出窗口中，选择 **"确认启用**"。

### <a name="increase-the-alert-grace-period"></a>增加警报宽限期

在 Ambari 代理报告配置的警报未按其计划之前，有一个宽限期。 如果警报未达到计划时间，但在宽限期内运行，则不会生成过期警报。

默认值`alert_grace_period`为 5 秒。 您可以在 /etc/ambari 代理/conf/ambari 代理中配置此设置。 对于定期发生过期警报的主机，请尝试将该值增加到 10。 然后，重新启动 Ambari 代理。

## <a name="next-steps"></a>后续步骤

如果您的问题在这里未被提及，或者您无法解决，请访问以下渠道之一，获得更多支持：

* 从[Azure 社区支持](https://azure.microsoft.com/support/community/)的 Azure 专家处获取答案。

* 在推特上联系[@AzureSupport](https://twitter.com/azuresupport)。 这是用于改善客户体验的官方 Microsoft Azure 帐户。 它将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，请从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 要达到此点，请从门户菜单中选择"帮助 **"**？ **Help + support** 有关详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 

  Microsoft Azure 订阅中包含对订阅管理和计费的支持。 技术支持可通过[Azure 支持计划](https://azure.microsoft.com/support/plans/)获得。
