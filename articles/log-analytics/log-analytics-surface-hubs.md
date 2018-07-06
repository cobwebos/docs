---
title: 使用 Azure Log Analytics 监视 Surface Hub | Microsoft 文档
description: 使用 Surface Hub 解决方案来跟踪 Surface Hub 的运行状况并了解它们的使用情况。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: dfbcdce293d6d47267892487d0760410665af94a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130764"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>使用 Log Analytics 监视 Surface Hub 以跟踪其运行状况

![“Surface Hub”符号](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

本文介绍如何使用 Log Analytics 中的 Surface Hub 解决方案来监视 Microsoft Surface Hub 设备。 Log Analytics 可帮助你跟踪 Surface Hub 的运行状况以及了解其使用情况。

每个 Surface Hub 都安装了 Microsoft Monitoring Agent。 通过代理，可以将数据从 Surface Hub 发送到 Log Analytics。 日志文件从 Surface Hub 读取，并发送到 Log Analytics。 类似于服务器处于脱机状态、日历不同步，或设备帐户无法登录 Skype 等这些问题都会显示在 Log Analytics 中的 Surface Hub 仪表板中。 通过使用仪表板中的数据，可以确定未运行或遇到其他问题的设备，并潜在解决检测到的问题。

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。 要在 Log Analytics 中管理 Surface Hub，需要以下项：

* [Log Analytics 订阅](https://azure.microsoft.com/pricing/details/log-analytics/)级别，用于支持要监视的设备数。 根据注册设备的数量以及处理的数据量，Log Analytics 定价会有所不同。 在规划 Surface Hub 的部署时需要考虑这一点。

添加一个现有的 Log Analytics 工作区，或新建一个工作区。 有关上述任一方法的详细使用说明，请参阅 [Log Analytics 入门](log-analytics-get-started.md)。 Log Analytics 工作区配置完成后，可通过两种方式注册 Surface Hub 设备：

* 通过 Intune 自动注册
* 通过 Surface Hub 设备上的“**设置**”手动注册。

## <a name="set-up-monitoring"></a>设置监视
可以使用 Log Analytics 监视 Surface Hub 的运行状况和活动。 可以使用 Intune 注册 Surface Hub，或在本地使用 Surface Hub 上的“设置”注册。

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>通过 Intune 将 Surface Hub 连接到 Log Analytics
要管理 Surface Hub，需要为 Log Analytics 工作区提供工作区 ID 和工作区密钥。 可从 Azure 门户的工作区设置中获取上述信息。

Intune 是 Microsoft 的一个产品，它允许集中管理应用于一个或多个设备的 Log Analytics 配置设置。 请按照以下步骤通过 Intune 配置设备：

1. 登录 Intune。
2. 导航到“**设置**” > “**连接源**”。
3. 创建或编辑基于 Surface Hub 模板的策略。
4. 导航到策略的 OMS（Azure 操作见解）部分，为策略添加 Log Analytics“工作区 ID”和“工作区密钥”。
5. 保存策略。
6. 关联策略和相应的设备组。

   ![Intune 策略](./media/log-analytics-surface-hubs/intune.png)

Intune 然后会在 Log Analytics 工作区中注册设备，从而将 Log Analytics 设置与目标组中的设备同步。

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>使用“设置”应用将 Surface Hub 连接到 Log Analytics
要管理 Surface Hub，需要为 Log Analytics 工作区提供工作区 ID 和工作区密钥。 可从 Azure 门户的 Log Analytics 工作区设置中获取上述信息。

如果不使用 Intune 管理环境，则可以通过每个 Surface Hub 上的“设置”手动注册设备：

1. 从 Surface Hub 打开“**设置**”。
2. 在出现提示时，输入设备管理凭据。
3. 单击“**此设备**”，并单击“**监视**”下面的“**配置 OMS 设置**”。
4. 选择“**启用监视**”。
5. 在“OMS 设置”对话框中，键入 Log Analytics“工作区 ID”和“工作区密钥”。  
   ![设置](./media/log-analytics-surface-hubs/settings.png)
6. 单击“**确定**”以完成配置。

将显示一条确认信息，告诉你是否已将配置成功应用于设备。 如果是，则会出现一条消息，注明代理已成功连接到 Log Analytics 服务。 然后，设备开始将数据发送到 Log Analytics，可以查看数据并采取相应操作。

## <a name="monitor-surface-hubs"></a>监视 Surface Hub
使用 Log Analytics 监视 Surface Hub 非常类似于监视任何其他已注册的设备。

1. 登录到 Azure 门户。
2. 导航至 Log Analytics 工作区并选择“概述”。
2. 单击 Surface Hub 磁贴。
3. 此时会显示设备的运行状况。

   ![Surface Hub 仪表板](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

可以根据现有或自定义的日志搜索创建[警报](log-analytics-alerts.md)。 通过使用 Log Analytics 从 Surface Hub 收集的数据，可以按照为设备定义的条件搜索问题和警报。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)，查看详细的 Surface Hub 数据。
* 创建[警报](log-analytics-alerts.md)，以便在 Surface Hub 出现问题时通知你。
