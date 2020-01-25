---
title: Azure HDInsight 中的 Apache Ambari 陈旧警报
description: HDInsight 中的陈旧 Apache Ambari 警报的可能原因和解决方法的讨论和分析。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722806"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>方案： Azure HDInsight 中的 Apache Ambari 陈旧警报

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>문제

在 Apache Ambari UI 中，你可能会看到类似于下图的警报：

![Apache Ambari 陈旧警报示例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>원인

Ambari 代理持续执行运行状况检查来监视多个资源的运行状况。 每个警报都配置为在预定义的时间间隔运行。 执行每个警报后，Ambari 代理会将状态报告回 Ambari 服务器。 此时，如果 Ambari server 检测到任何警报不能及时运行，则会触发 "Ambari 服务器警报"。 运行状况检查可能无法在其定义的时间间隔内执行的原因有多种：

* 当主机的使用率很高（CPU 使用率高）时，Ambari 代理可能无法及时获得足够的系统资源来及时执行警报。

* 在繁重负载期间，群集正在忙于执行许多作业/服务。

* 群集中的少数主机可能会托管多个组件，因此需要运行多个警报。 如果组件数量很大，警报作业可能会错过其计划间隔

## <a name="resolution"></a>해상도

### <a name="increase-alert-interval-time"></a>增加警报间隔时间

您可以选择根据群集的响应时间和负载增加单个警报间隔的值。

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 选择所需的警报定义名称。
1. 从定义中选择 "**编辑**"。
1. 根据需要修改 "**检查间隔**" 值，然后选择 "**保存**"。

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>增加 Ambari 服务器警报的警报间隔时间

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 从 "**组**" 下拉列表中，选择 " **AMBARI 默认值**"。
1. 选择 "警报**Ambari 服务器警报**"。
1. 从定义中选择 "**编辑**"。
1. 根据需要修改**检查间隔**值。
1. 根据需要修改**间隔乘数**值，然后选择 "**保存**"。

### <a name="disable-and-enable-the-alert"></a>禁用并启用警报

你可以禁用，然后再次启用该警报以丢弃任何过时警报。

1. 在 Apache Ambari UI 中，选择 "**警报**" 选项卡。
1. 选择所需的警报定义名称。
1. 从定义中选择 "**已启用**" （位于最右侧）。
1. 从**确认**弹出窗口中，选择 "**确认禁用**"。
1. 请等待几秒钟，以便清除该页上显示的所有警报 "实例"。
1. 从定义中选择 "**已禁用**" （位于最右侧）。
1. 从**确认**弹出窗口中，选择 "**确认启用**"。

### <a name="increase-alert-grace-time"></a>增加警报宽限期

在 Ambari 代理报告已配置的警报丢失其计划之前，会应用一个宽限期。 即使警报丢失了计划时间，但在警报宽限期内触发了警报，也不会触发过时警报。

默认 `alert_grace_period` 值为5秒。 此 `alert_grace_period` 设置在 `/etc/ambari-agent/conf/ambari-agent.ini`中是可配置的。 对于定期触发陈旧警报的那些主机，请尝试将值增加到10。 然后重新启动 Ambari 代理

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
