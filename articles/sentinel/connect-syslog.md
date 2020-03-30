---
title: 将 Syslog 数据连接到 Azure 哨兵 |微软文档
description: 了解如何将 Syslog 数据连接到 Azure 哨兵。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588070"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 连接外部解决方案

您可以将任何支持 Syslog 的本地设备连接到 Azure Sentinel。 这是通过使用基于设备和 Azure Sentinel 之间的 Linux 计算机的代理来完成的。 如果 Linux 计算机位于 Azure 中，则可以将日志从设备或应用程序流式传输到在 Azure 中创建的专用工作区并连接它。 如果 Linux 计算机不在 Azure 中，则可以将日志从设备流式传输到安装 Linux 代理的专用本地 VM 或计算机。 

> [!NOTE]
> 如果您的设备支持 Syslog CEF，则连接更加完整，您应该选择此选项，并按照 CEF 中的["连接数据"](connect-common-event-format.md)中的说明进行操作。

## <a name="how-it-works"></a>工作原理

Syslog 是普遍适用于 Linux 的事件日志记录协议。 应用程序将发送可能存储在本地计算机或传递到 Syslog 收集器的消息。 安装适用于 Linux 的 Log Analytics 代理后，它将配置本地 Syslog 后台程序，以将消息转发到此代理。 然后，此代理将消息发送到 Azure Monitor，将在后者中创建相应的记录。

有关详细信息，请参阅[Azure 监视器 中的 Syslog 数据源](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> - 代理可以从多个源收集日志，但必须安装在专用代理计算机上。
> - 如果要在同一 VM 上支持 CEF 和 Syslog 的连接器，请执行以下步骤以避免重复数据：
>    1. 按照说明[连接您的CEF。](connect-common-event-format.md)
>    2. 要连接 Syslog 数据，请**转到"设置** > **工作区设置** > **高级设置** > **""数据** > **系统日志**并设置设施及其优先级，使其与您在 CEF 配置中使用的设施和属性不同。 <br></br>如果选择 **"将下面的配置应用于我的计算机**"，它将这些设置应用于连接到此工作区的所有 VM。


## <a name="connect-your-syslog-appliance"></a>连接 Syslog 设备

1. 在 Azure 哨兵中，选择**数据连接器**，然后选择**Syslog**连接器。

2. 在**Syslog**边栏选项卡上，选择 **"打开连接器"页**。

3. 安装 Linux 代理：
    
    - 如果 Linux 虚拟机位于 Azure 中，请选择**Azure Linux 虚拟机上的"下载"和"安装代理**"。 在**虚拟机**边栏选项卡中，选择要安装代理的虚拟机，然后单击"**连接**"。
    - 如果 Linux 计算机不在 Azure 中，请选择**Linux 非 Azure 计算机上的"下载"和"安装代理**"。 在**直接代理**边栏选项卡中，复制**LINUX 的下载和 ONBOARD 代理**命令，并在您的计算机上运行它。 
    
   > [!NOTE]
   > 请确保根据组织的安全策略为这些计算机配置安全设置。 例如，您可以将网络设置配置为与组织的网络安全策略保持一致，并更改守护进程中的端口和协议以符合安全要求。

4. 选择 **"打开工作区高级设置配置**"。

5. 在 **"高级设置"** 边栏选项卡上，选择 **"数据** > **系统"。** 然后添加要收集的连接器的设施。
    
    添加 syslog 设备在其日志标头中包括的设施。 您可以在`/etc/rsyslog.d/security-config-omsagent.conf`文件夹中**的 Syslog-d**中的 Syslog 设备中以及 中的`/etc/syslog-ng/security-config-omsagent.conf` **r-Syslog**中看到此配置。
    
    如果要使用异常的 SSH 登录检测与您收集的数据，添加**auth**和**authpriv**。 有关详细信息，请参阅[以下部分](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

6. 添加要监视的所有设施，并为每个功能调整任何严重性选项后，选择"**将下面的配置应用于我的计算机**"复选框。

7. 选择“保存”。**** 

8. 在 syslog 设备上，请确保发送指定的设施。

9. 要在 Azure 监视器中使用相关架构进行系统日志，请搜索**Syslog**。

10. 可以使用[Azure 监视器日志查询中的"使用函数"中描述的](../azure-monitor/log-query/functions.md)Kusto 函数来分析 Syslog 消息。 然后，您可以将它们另存为新的日志分析函数，以用作新的数据类型。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>配置 Syslog 连接器以进行异常 SSH 登录检测

> [!IMPORTANT]
> 异常 SSH 登录检测当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以将机器学习 （ML） 应用于系统日志数据，以识别异常的安全外壳 （SSH） 登录活动。 方案包括：

- 不可能的旅行 - 当两个成功的登录事件从两个位置发生，不可能在两个登录事件的时间范围内到达。
- 意外位置 – 成功发生登录事件的位置可疑。 例如，最近未看到该位置。
 
此检测需要 Syslog 数据连接器的特定配置： 

1. 对于前一过程的步骤 5，请确保选择**auth**和**authpriv**作为要监视的设施。 保留严重性选项的默认设置，以便全部选中。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![异常 SSH 登录检测所需的设施](./media/connect-syslog/facilities-ssh-detection.png)

2. 留出足够的时间收集系统日志信息。 然后，导航到**Azure Sentinel - 日志**，然后复制并粘贴以下查询：
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    如果需要，更改**时间范围**，然后选择 **"运行**"。
    
    如果生成的计数为零，请确认连接器的配置，并且受监视的计算机确实具有为查询指定的时间段内成功的登录活动。
    
    如果生成的计数大于零，则 syslog 数据适用于异常的 SSH 登录检测。 您启用此检测从**分析** >  **规则模板** > **（预览）异常 SSH 登录检测**。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Syslog 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

