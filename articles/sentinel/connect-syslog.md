---
title: 将 Syslog 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Syslog 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588070"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 连接外部解决方案

可以将支持 Syslog 的任何本地设备连接到 Azure Sentinel。 这是通过在设备和 Azure Sentinel 之间使用基于 Linux 计算机的代理来完成的。 如果 Linux 计算机位于 Azure 中，则可以将设备或应用程序中的日志流式传输到在 Azure 中创建的专用工作区，并进行连接。 如果 Linux 计算机不在 Azure 中，则可以将设备中的日志流式传输到专用的本地 VM 或计算机上，以便在其中安装适用于 Linux 的代理。 

> [!NOTE]
> 如果设备支持 Syslog CEF，则连接将更完整，应选择此选项，并按照[连接来自 CEF 的数据](connect-common-event-format.md)中的说明进行操作。

## <a name="how-it-works"></a>工作原理

Syslog 是普遍适用于 Linux 的事件日志记录协议。 应用程序将发送可能存储在本地计算机或传递到 Syslog 收集器的消息。 安装适用于 Linux 的 Log Analytics 代理后，它将配置本地 Syslog 后台程序，以将消息转发到此代理。 然后，此代理将消息发送到 Azure Monitor，将在后者中创建相应的记录。

有关详细信息，请参阅[中的 Syslog 数据源 Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> - 代理可以从多个源收集日志，但必须在专用代理计算机上安装日志。
> - 如果要在同一 VM 上同时支持 CEF 和 Syslog 的连接器，请执行以下步骤以避免复制数据：
>    1. 按照说明连接到[CEF](connect-common-event-format.md)。
>    2. 若要连接 Syslog 数据，请参阅 "**设置**" > "**工作区设置**" > "**高级设置**" " > **数据**" > **Syslog** "，并设置设备及其优先级，以便它们不同于 CEF 配置中使用的设备和属性。 <br></br>如果选择 "**将下面的配置应用到我的计算机**"，则会将这些设置应用到连接到此工作区的所有 vm。


## <a name="connect-your-syslog-appliance"></a>连接 Syslog 设备

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后选择**Syslog**连接器。

2. 在**Syslog**边栏选项卡上，选择 "**打开连接器" 页面**。

3. 安装 Linux 代理：
    
    - 如果 Linux 虚拟机位于 Azure 中，请选择 **"在 Azure Linux 虚拟机上下载并安装代理"** 。 在 "**虚拟机**" 边栏选项卡中，选择要在其上安装代理的虚拟机，然后单击 "**连接**"。
    - 如果 Linux 计算机不在 Azure 中，请选择 **"在 linux 非 azure 计算机上下载并安装代理"** 。 在 "**直接代理**" 边栏选项卡中，复制**下载和板载 agent for LINUX**的命令并在计算机上运行该命令。 
    
   > [!NOTE]
   > 请确保根据组织的安全策略配置这些计算机的安全设置。 例如，你可以配置网络设置，使其符合组织的网络安全策略，并更改守护程序中的端口和协议，使其符合安全要求。

4. 选择 "**打开工作区高级设置配置**"。

5. 在 "**高级设置**" 边栏选项卡中，选择 " **Data** > **Syslog**"。 然后，添加要收集的连接器的功能。
    
    添加 syslog 设备在其日志标头中包含的工具。 你可以在 syslog 设备的 syslog **-d**中的 "`/etc/rsyslog.d/security-config-omsagent.conf`" 文件夹中以及从 `/etc/syslog-ng/security-config-omsagent.conf`的**r-syslog**中查看此配置。
    
    如果要将异常 SSH 登录检测与收集的数据一起使用，请添加**auth**和**authpriv**。 有关更多详细信息，请参阅[以下部分](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

6. 如果已添加要监视的所有设备，并调整每个设备的任何严重性选项，请选中 "**将下面的配置应用到我的计算机**" 复选框。

7. 选择“保存”。 

8. 在 syslog 设备上，确保正在发送指定的设施。

9. 若要在 syslog 日志的 Azure Monitor 中使用相关架构，请搜索**syslog**。

10. 您可以使用[Azure Monitor 日志查询中的函数中](../azure-monitor/log-query/functions.md)所述的 Kusto 函数来分析 Syslog 消息。 然后，您可以将其另存为新的 Log Analytics 函数，用作一种新的数据类型。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>为异常 SSH 登录检测配置 Syslog 连接器

> [!IMPORTANT]
> 异常 SSH 登录检测目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以将机器学习（ML）应用于 syslog 数据，以确定异常安全外壳（SSH）登录活动。 方案包括：

- 不可能的旅行–当两个成功登录事件发生在两个位置，而这两个位置无法在两个登录事件的时间范围内到达时。
- 意外位置–成功登录事件发生的位置可疑。 例如，最近未出现位置。
 
此检测需要 Syslog 数据连接器的特定配置： 

1. 对于前一过程中的步骤5，请确保选择 "**身份验证**" 和 " **authpriv** " 作为要监视的设施。 保留 "严重性" 选项的默认设置，以便所有这些选项都处于选中状态。 例如：
    
    > [!div class="mx-imgBorder"]
    > 异常 SSH 登录检测所需的 ![设施](./media/connect-syslog/facilities-ssh-detection.png)

2. 留出足够的时间来收集 syslog 信息。 然后，导航到 " **Azure Sentinel 日志**"，复制并粘贴以下查询：
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    更改**时间范围**（如果需要），然后选择 "**运行**"。
    
    如果生成的计数为零，请确认连接器的配置，并且被监视的计算机在您为查询指定的时间段内具有成功的登录活动。
    
    如果生成的计数大于零，则 syslog 数据适用于异常 SSH 登录检测。 ** >  ** **规则模板** >  **（预览）异常 SSH 登录检测**启用此检测。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Syslog 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

