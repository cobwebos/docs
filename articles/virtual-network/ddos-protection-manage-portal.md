---
title: "使用 Azure 门户管理 Azure DDoS 保护标准 | Microsoft Docs"
description: "了解如何在 Azure Monitor 中使用 Azure DDoS 保护标准遥测来缓解攻击。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2b55341a9d5107376557147913f4fb6cb3966bae
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>使用 Azure 门户管理 Azure DDoS 保护标准

>[!IMPORTANT]
>Azure DDoS 保护标准（DDoS 保护）当前为预览版。 有限数量的 Azure 资源在选定的区域中支持 DDoS 保护。 在使用受限的预览版期间，需要[针对该服务进行注册](http://aka.ms/ddosprotection)才能为订阅启用 DDoS 保护。 注册后，Azure DDoS 团队会联系你以指导你完成启用过程。 DDoS 保护仅在美国东部、美国西部和美国中部区域可用。 在预览版期间，使用该服务不用付费。

这篇文章演示了如何使用 Azure 门户来启用 DDoS 保护、禁用 DDoS 保护以及如何使用遥测来缓解攻击。 

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="enable-ddos-protection"></a>启用 DDoS 保护

在新的或现有虚拟网络上启用 DDoS 保护。

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>创建新的虚拟网络并启用 DDoS 保护

1. 单击 Azure 门户左上角的“新建”按钮。
2. 选择“网络”，然后选择“虚拟网络”。
3. 输入虚拟网络信息。 在“DDoS 保护”下，单击“已启用”，然后单击“创建”。

    ![创建虚拟网络](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

一条警告将声明启用 DDoS 保护会产生费用。 在预览版期间，DDoS 保护不收费。 公开上市 (GA) 时将收费，并且客户会在开始收费且公开上市前 30 天收到通知。

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>在现有虚拟网络上启用 DDoS 保护 

1. 在 Azure 门户菜单中单击“虚拟网络”，然后选择你的虚拟网络。
2. 单击“DDoS 保护”，在“DDoS 保护”屏幕上单击“已启用”，然后单击“保存”。 

一条警告将声明启用 DDoS 保护会产生费用。 在预览版期间，DDoS 保护不收费。 公开上市 (GA) 时将收费，并且客户会在开始收费且公开上市前 30 天收到通知。

## <a name="disable-ddos-protection"></a>禁用 DDoS 保护

1. 在 Azure 门户菜单中单击“虚拟网络”，然后选择你的虚拟网络。
2. 单击“DDoS 保护”，在“DDoS 保护”屏幕上单击“已禁用”，然后单击“保存”。

## <a name="configure-alerts-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 保护指标以在攻击期间发生活跃的风险缓解时发出警报。 当满足条件时，会在指定的地址收到警报电子邮件。

1. 单击“监视器”，然后单击“指标”。
2. 在“指标”屏幕上，选择资源组、资源类型“公用 IP 地址”和你的 Azure 公用 IP。
3. 若要针对某个指标配置电子邮件警报，请单击“单击以添加警报”。 可以针对任何指标创建电子邮件警报，但最明显的指标为“是否遭到 DDoS 攻击”。 这是一个为 1 或 0 的布尔值。 **1** 表示遭到攻击。 **0** 表示未遭到攻击。
4. 若要在遭到攻击时收到电子邮件，请针对“是否遭到 DDoS 攻击”和“过去 5 分钟内大于零 (0) 的情况”设置指标。 可以针对其他指标设置类似警报。

    ![配置指标](./media/ddos-protection-manage-portal/ddos-metrics.png)

    在检测到攻击的几分钟内，会使用 Azure Monitor 指标向你发送通知。

    ![攻击警报](./media/ddos-protection-manage-portal/ddos-alert.png) 

还可以了解有关为创建警报而[配置 webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) 和[逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)的详细信息。

## <a name="configure-logging-on-ddos-protection-metrics"></a>配置针对 DDoS 保护指标的日志记录

1. 单击“监视器”，然后单击“诊断设置”。
2. 在“指标”屏幕上，选择资源组、资源类型“公用 IP 地址”和你的 Azure 公用 IP。
3. 单击“打开诊断以收集以下数据”。

为日志记录提供了三个选项：

- **存档到存储帐户** – 将日志写入到一个存储帐户。
- **流式传输到事件中心** –  允许日志接收器使用事件中心选取日志。 这将启用与 Splunk 或其他 SIEM 系统的集成。
- **发送到 Log Analytics** – 将日志写入到 Azure OMS Log Analytics 服务。

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 保护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公用 IP 地址处于风险缓解的期间可用。 在攻击缓解之前或之后，都不会看到遥测。

1. 单击“监视器”，然后单击“指标”。 
2. 在“指标”屏幕上，选择资源组、资源类型“公用 IP 地址”和你的 Azure 公用 IP。 屏幕的左侧将显示一系列可用指标。 当选中时，将在概述屏幕上的 Azure Monitor 指标图表中绘制这些指标。 

指标名称提供不同的数据包类型和字节数与数据包数，每个指标都有如下所示的标记名称基本构造：

- **“丢弃的”标记名称（例如，丢弃的入站数据包 DDoS）：**由 DDoS 保护系统丢弃/清理的数据包数。
- **“转发的”标记名称（例如，转发的入站数据包 DDoS）：**由 DDoS 系统转发到目标 VIP 的数据包数 – 未筛选的流量。
- **无标记名称（例如，入站数据包 DDoS）：**进入到清理系统的总数据包数 – 表示丢弃的和转发的数据包的和。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)
- [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
