---
title: 将 Syslog 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何将 Syslog 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679207"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 连接外部解决方案

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以将支持 Syslog 的任何本地设备连接到 Azure Sentinel。 这是通过在设备和 Azure Sentinel 之间使用基于 Linux 计算机的代理来完成的。 如果 Linux 计算机位于 Azure 中, 则可以将设备或应用程序中的日志流式传输到在 Azure 中创建的专用工作区, 并进行连接。 如果 Linux 计算机不在 Azure 中, 则可以将设备中的日志流式传输到专用的本地 VM 或计算机上, 以便在其中安装适用于 Linux 的代理。 

> [!NOTE]
> 如果设备支持 Syslog CEF, 则连接将更完整, 应选择此选项, 并按照[连接来自 CEF 的数据](connect-common-event-format.md)中的说明进行操作。

## <a name="how-it-works"></a>工作原理

Syslog 是普遍适用于 Linux 的事件日志记录协议。 应用程序将发送可能存储在本地计算机或传递到 Syslog 收集器的消息。 安装适用于 Linux 的 Log Analytics 代理后，它将配置本地 Syslog 后台程序，以将消息转发到此代理。 然后，此代理将消息发送到 Azure Monitor，将在后者中创建相应的记录。

有关详细信息, 请参阅[中的 Syslog 数据源 Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> 代理可以从多个源收集日志, 但必须在专用代理计算机上安装日志。

## <a name="connect-your-syslog-appliance"></a>连接 Syslog 设备

1. 在 Azure Sentinel 门户中, 选择 "**数据连接器**", 并在表中选择**syslog**行, 并在右侧的 syslog 窗格中单击 "**打开连接器" 页面**。
2. 如果 Linux 计算机位于 Azure 中, 请选择 **"在 Azure Linux 虚拟机上下载并安装代理"** 。 在 "虚拟机" 窗口中, 选择要在其上安装代理的计算机, 然后单击顶部的 "**连接**"。
1. 如果 Linux 计算机不在 Azure 中, 请选择 **"在 linux 非 azure 计算机上下载并安装代理"** 。 在 "**直接代理**" 窗口中, 复制 "**下载和板载 agent for Linux** " 下的命令并在计算机上运行。 
   > [!NOTE]
   > 请确保根据组织的安全策略配置计算机的安全性。 例如, 你可以将网络配置为与你的企业网络安全策略一致, 并更改守护程序中的端口和协议以符合你的要求。 

1. 在 "**配置要连接的日志**" 下, 在 Syslog 连接器设置窗口中, 按照说明进行操作:
    1. 单击链接以**打开工作区高级设置配置**。 
    1. 选择 "**数据**", 然后选择 " **Syslog**"。
    1. 然后, 在下表中设置希望 Syslog 收集的设施。 应添加或选择 Syslog 设备在其日志标头中包含的工具。 你可以在 syslog-d 中的/etc/rsyslog.d/security-config-omsagent.conf 和/etc/syslog-ng/security-config-omsagent.conf. 下的 r-Syslog 中查看 Syslog 设备中的此配置。 
       > [!NOTE]
       > 如果选中 "**将下面的配置应用到我的计算机**" 复选框, 则此配置将应用于所有连接到此工作区的 Linux 计算机。 你可以在 Syslog 计算机上的 
1. 单击**此处以打开 "配置" 边栏选项卡**。
1. 选择 "**数据**", 然后选择 " **Syslog**"。
   - 请确保 Syslog 要发送的每个设备都在表中。 对于每个设施, 将监视, 设置严重性。 单击“应用”。
1. 在 Syslog 计算机上, 请确保正在发送这些设施。 

1. 若要在 Syslog 日志的 Log Analytics 中使用相关架构, 请搜索**syslog**。
1. 您可以使用[Azure Monitor 日志查询中的函数使用](../azure-monitor/log-query/functions.md)中所述的 Kusto 函数来分析 Syslog 消息, 然后将其保存为新的 Log Analytics 函数, 然后使用函数作为新的数据类型。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Syslog 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
