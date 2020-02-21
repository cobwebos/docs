---
title: Azure Sentinel 中的高级多阶段攻击检测
description: 使用 Azure Sentinel 中的合成技术来减少警报疲劳，并创建基于高级多阶段攻击检测的可操作事件。
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: 5ab5d3c0fc1c37feaac2cc6b4b6837627c5a82df
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500643"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的高级多阶段攻击检测


> [!IMPORTANT]
> Azure Sentinel 中的某些合成功能当前以公共预览版提供。
> 提供这些功能没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



通过使用基于机器学习的合成技术，Azure Sentinel 可以通过组合在终止链的不同阶段观察到的异常行为和可疑活动，来自动检测多阶段攻击。 然后，Azure Sentinel 会生成可能非常难以捕获的事件。 这些事件以及括住了两个或多个警报或活动。 按照设计，这些事件的数量较低、高度保真和高严重性。

针对你的环境进行了自定义，此检测不仅可减少误报速率，而且还可以检测具有有限或缺失信息的攻击。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>高级多阶段攻击检测的配置

默认情况下，Azure Sentinel 中启用此检测。 若要检查状态或禁用该状态，可能是因为你使用替代解决方案来基于多个警报创建事件，请使用以下说明：

1. 如果尚未登录 [Azure 门户](https://portal.azure.com)，请先登录。

2. 导航到**Azure Sentinel** > **配置** > **Analytics**

3. 选择 "**活动规则**" 并在 "**名称**" 列中找到 "**高级多阶段攻击检测**"。 检查 "**状态**" 列以确认是否已启用或禁用此检测。

4. 若要更改状态，请选择此项，然后在 "**高级多阶段攻击检测**" 边栏选项卡上，选择 "**编辑**"。

5. 在**规则创建向导**边栏选项卡上，会自动选择状态更改，因此请选择 "**下一步：查看**"，然后单击 "**保存**"。 

规则模板不适用于高级多阶段攻击检测。

> [!NOTE]
> Azure Sentinel 目前使用30天的历史数据来训练机器学习系统。 此数据将在通过机器学习管道传递时使用 Microsoft 密钥进行加密。 但是，如果在 Azure Sentinel 工作区中启用了 CMK，则不会使用[客户托管的密钥（CMK）](customer-managed-keys.md)加密定型数据。 若要选择退出，请导航到**Azure Sentinel** \> **配置** \> **Analytics \> Active Rules \> Advanced 多阶段攻击检测**"，然后在"**状态**"列中，选择"**禁用 "。**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>使用 Palo Alto 网络和 Microsoft Defender ATP 进行合成

这些方案结合了安全分析师使用的两个基本日志：来自 Palo Alto 网络的防火墙日志和来自 Microsoft Defender ATP 的终结点检测日志。 在下面列出的所有方案中，在涉及外部 IP 地址的终结点中检测到可疑活动，然后将来自外部 IP 地址的异常流量返回到防火墙中。 在 Palo Alto 日志中，Azure Sentinel 侧重于[威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，在允许威胁时（可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires），流量被视为可疑。

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名服务的网络请求，后跟 Palo Alto 网络防火墙标记的异常流量。

在此方案中，Azure Sentinel 首先检测到 Microsoft Defender 高级威胁防护检测到对 TOR 匿名服务的网络请求，导致异常活动。 此操作是在帐户 {account name} 下启动的，SID ID {sid} 于 {time}。 连接的传出 IP 地址是 {IndividualIp}。
然后，Palo Alto 网络防火墙在 {TimeGenerated} 检测到异常活动。 这表明网络流量的目标 IP 地址为 {DestinationIP}。

此方案目前为公共预览版。


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 进行了可疑的网络连接，并 Palo Alto 网络防火墙标记的异常流量。

在此方案中，Azure Sentinel 首先检测到 Microsoft Defender 高级威胁防护检测到 PowerShell 发出可疑网络连接，导致 Palo Alto 网络防火墙检测到异常活动。 这是由在 {time} 的 SID ID 为 {sid} 的帐户 {account name} 启动的。 连接的传出 IP 地址是 {IndividualIp}。 然后，Palo Alto 网络防火墙在 {TimeGenerated} 检测到异常活动。 这表明网络流量进入了网络。 网络流量的目标 IP 地址为 {DestinationIP}。

此方案目前为公共预览版。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>到 IP 的出站连接，其中包含未经授权的访问尝试的历史记录，以及 Palo Alto 网络防火墙标记的异常流量

在这种情况下，Azure Sentinel 会检测到警报，指出 Microsoft Defender 高级威胁防护检测到 IP 地址的出站连接，其中包含导致 Palo Alto 检测到异常活动的未经授权的访问尝试。网络防火墙。 这是由在 {time} 的 SID ID 为 {sid} 的帐户 {account name} 启动的。 连接的传出 IP 地址是 {IndividualIp}。 完成此操作后，Palo Alto 网络防火墙在 {TimeGenerated} 检测到异常活动。 这表明网络流量进入了网络。 网络流量的目标 IP 地址为 {DestinationIP}。

此方案目前为公共预览版。



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>使用 Identity Protection 和 Microsoft Cloud App Security 进行合成

使用高级多阶段攻击检测时，Azure Sentinel 支持以下将异常事件与 Azure Active Directory Identity Protection 和 Microsoft Cloud App Security 组合在一起的方案：

- [不可能前往异常位置，然后再执行异常 Office 365 活动](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [不熟悉的位置的登录活动，后跟反常的 Office 365 活动](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [来自受感染设备的登录活动，后跟异常 Office 365 活动](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [来自匿名 IP 地址的登录活动，后跟异常 Office 365 活动](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [用户的登录活动，该用户的凭据已泄露，后跟异常 Office 365 活动](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

您必须配置[Azure AD Identity Protection 数据连接器](connect-azure-ad-identity-protection.md)和[Cloud App Security](connect-cloud-app-security.md)连接器。

在接下来的说明中，Azure Sentinel 会将此页上表示的数据的实际值显示为方括号中的变量。 例如，帐户的实际显示名称（而不是 \<*帐户名*>，而不是 \<*数字*> 的实际数字。

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>不可能前往异常位置，然后再执行异常 Office 365 活动

有7个可能的 Azure Sentinel 事件，它们将不可能从 Microsoft Cloud App Security 生成的 Azure AD Identity Protection 和异常的 Office 365 警报合并到异常位置警报：

- **不可能前往导致 Office 365 邮箱渗透的异常位置**
    
    此警报是由于在用户的收件箱中设置了一个不可能旅行到 \<*位置*>、一个非典型位置，然后有一个可疑的收件箱转发规则 \<*帐户名*> 的登录事件的指示。
    
    这可能表示帐户已泄露，并且邮箱正在用于盗取你的组织中的信息。 用户 \<*帐户名称*> 创建或更新了收件箱转发规则，该规则将所有传入电子邮件转发到外部地址 \<*电子邮件地址*>。

- **不可能旅行到异常位置，导致云应用管理活动可疑**
    
    此警报是 \<*帐户名*> 从不可能传播到 \<*位置*> （非典型位置）的登录事件的指示。
    
    接下来，帐户 \<*帐户名称*> 在单个会话中通过 \<*号*> 管理活动执行。

- **不可能前往异常位置，导致批量文件删除**
    
    此警报是通过 \<*帐户名*> 到 \<*位置*> 典型位置来指示登录事件。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<> 唯一文件的*数量*。

- **不可能前往导致大容量文件下载的异常位置**
    
    此警报是 \<*帐户名*> 从不可能传播到 \<*位置*> （非典型位置）的登录事件的指示。 
    
    接下来，在单个会话中通过 \<*数量的*> 唯一文件 > 下载帐户 \<*帐户名称*。

- **不可能前往导致 Office 365 模拟的异常位置**
    
    此警报是 \<*帐户名*> 从不可能传播到 \<*位置*> （非典型位置）的登录事件的指示。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话中执行模拟活动的异常量（\<*活动*>）。

- **不可能前往导致大容量文件共享的异常位置**
    
    此警报是 \<*帐户名*> 从不可能传播到 \<*位置*> （非典型位置）的登录事件的指示。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<>*的*唯一文件之间共享。

- **不可能前往导致云应用中的勒索软件的异常位置**
    
    此警报是 \<*帐户名*> 从不可能传播到 \<*位置*> （非典型位置）的登录事件的指示。 
    
    接下来，帐户 \<*帐户名称*> 上传 \<> 文件*数*，并删除 \<*个*文件的总数。 
    
    此活动模式表示存在潜在的勒索软件攻击。


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>不熟悉的位置的登录活动，后跟反常的 Office 365 活动

有7个可能的 Azure Sentinel 事件，这些事件结合了 Microsoft Cloud App Security 生成的 Azure AD Identity Protection 和异常的 Office 365 警报中的 "不熟悉的位置" 警报的登录活动。

- **从不熟悉的位置登录事件，导致 Exchange Online 邮箱渗透**
    
    此警报是指在用户的收件箱中，通过 \<*位置*> \<*帐户名称*>、不熟悉的位置，然后设置了可疑的收件箱转发规则，来指示登录事件。
    
    这可能表示帐户已泄露，并且邮箱正在用于盗取你的组织中的信息。 用户 \<*帐户名称*> 创建或更新了收件箱转发规则，该规则将所有传入电子邮件转发到外部地址 \<*电子邮件地址*>。 

- **来自不熟悉的位置的登录事件，导致云应用管理活动可疑**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话*中 \<>* 管理活动执行。

- **从不熟悉的位置登录事件，导致批量文件删除**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<> 唯一文件的*数量*。

- **从不熟悉的位置登录事件，导致批量文件下载**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。 
    
    接下来，在单个会话中通过 \<*数量的*> 唯一文件 > 下载帐户 \<*帐户名称*。

- **从通向 Office 365 模拟的不熟悉的位置登录事件**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。
    
    接下来，帐户 \<*帐户名称*> 在单个会话中 \<*数量的*> 不同帐户上模拟。

- **从不熟悉的位置登录事件，导致批量文件共享**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<>*的*唯一文件之间共享。

- **从不熟悉的位置登录事件，导致云应用中的勒索软件**
    
    此警报是通过从 \<*位置*> \<*帐户名*> 来表明登录事件，这是一个不熟悉的位置。 
    
    接下来，帐户 \<*帐户名称*> 上传 \<> 文件*数*，并删除 \<*个*文件的总数。 
    
    此活动模式表示存在潜在的勒索软件攻击。

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>来自受感染设备的登录活动，后跟异常 Office 365 活动

有7个可能的 Azure Sentinel 事件，这些事件将受感染设备警报中的登录活动与 Microsoft Cloud App Security 生成的 Azure AD Identity Protection 和异常 Office 365 警报合并：

- **来自受感染设备的登录事件，导致 Office 365 邮箱渗透**
    
    此警报是通过 \<可能感染了恶意软件的设备中的*帐户名*>，然后在用户的收件箱中设置了可疑的收件箱转发规则来指示登录事件。
    
    这可能表示帐户已泄露，并且邮箱正在用于盗取你的组织中的信息。 用户 \<*帐户名称*> 创建或更新了收件箱转发规则，该规则将所有传入电子邮件转发到外部地址 \<*电子邮件地址*>。 

- **来自受感染设备的登录事件，导致了可疑的云应用管理活动**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。
    
    接下来，帐户 \<*帐户名称*> 在单个会话*中 \<>* 管理活动执行。

- **来自受感染设备的登录事件，导致批量文件删除**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<> 唯一文件的*数量*。

- **来自受感染设备的登录事件，导致批量文件下载**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。 
    
    接下来，在单个会话中通过 \<*数量的*> 唯一文件 > 下载帐户 \<*帐户名称*。

- **来自受感染设备的登录事件，导致 Office 365 模拟**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话中 \<*数量的*> 不同帐户上模拟。

- **来自受感染设备的登录事件，导致批量文件共享**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<>*的*唯一文件之间共享。

- **从受感染的设备登录到云应用中的勒索软件的登录事件**
    
    此警报是通过 \<可能感染恶意软件的设备中的*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名称*> 上传 \<> 文件*数*，并删除 \<*个*文件的总数。 
    
    此活动模式表示存在潜在的勒索软件攻击。

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>来自匿名 IP 地址的登录活动，后跟异常 Office 365 活动

有7个可能的 Azure Sentinel 事件，这些事件结合了来自 Microsoft Cloud App Security 生成的 Azure AD Identity Protection 和异常的 Office 365 警报中的匿名 IP 地址警报的登录活动：

- **从通向 Office 365 邮箱渗透的匿名 IP 地址登录事件**
    
    此警报是通过将匿名代理 IP 地址 \<*ip 地址*>，然后在用户的收件箱中设置了可疑的收件箱转发规则 \<*帐户名*> 来指示登录事件。
    
    这可能表示帐户已泄露，并且邮箱正在用于盗取你的组织中的信息。 用户 \<*帐户名称*> 创建或更新了收件箱转发规则，该规则将所有传入电子邮件转发到外部地址 \<*电子邮件地址*>。 

- **来自匿名 IP 地址的登录事件，导致云应用管理活动可疑**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话*中 \<>* 管理活动执行。

- **从导致批量文件删除的匿名 IP 地址登录事件**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<> 唯一文件的*数量*。

- **从通向批量文件下载的匿名 IP 地址登录事件**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，在单个会话中通过 \<*数量的*> 唯一文件 > 下载帐户 \<*帐户名称*。

- **从通向 Office 365 模拟的匿名 IP 地址登录事件**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话中 \<*数量的*> 不同帐户上模拟。

- **从导致批量文件共享的匿名 IP 地址登录事件**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<>*的*唯一文件之间共享。

- **从匿名 IP 地址登录到云应用中的勒索软件的登录事件**
    
    此警报是通过从匿名代理 IP 地址 \<*ip 地址*> 中 \<*帐户名*> 来指示登录事件。 
    
    接下来，帐户 \<*帐户名称*> 上传 \<> 文件*数*，并删除 \<*个*文件的总数。 
    
    此活动模式表示存在潜在的勒索软件攻击。

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>用户的登录活动，该用户的凭据已泄露，后跟异常 Office 365 活动

有7个可能的 Azure Sentinel 事件，这些事件将来自用户的登录活动与 Microsoft Cloud App Security 生成的 Azure AD Identity Protection 和异常的 Office 365 警报相结合：

- **用户的登录事件，其凭据已泄露，导致 Office 365 邮箱渗透**
    
    此警报指示 \<*帐户名称*> 使用泄漏的凭据的登录事件，并在用户的收件箱中设置了可疑的收件箱转发规则。 
    
    这可能表示帐户已泄露，并且邮箱正在用于盗取你的组织中的信息。 用户 \<*帐户名称*> 创建或更新了收件箱转发规则，该规则将所有传入电子邮件转发到外部地址 \<*电子邮件地址*>。 

- **用户的登录事件，该用户的凭据已泄露，导致云应用管理活动可疑**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。
    
    接下来，帐户 \<*帐户名称*> 在单个会话*中 \<>* 管理活动执行。

- **用户的登录事件，其凭据已泄露，导致批量文件删除**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<> 唯一文件的*数量*。

- **用户的登录事件，其凭据已泄露，导致批量文件下载**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。
    
    接下来，在单个会话中通过 \<*数量的*> 唯一文件 > 下载帐户 \<*帐户名称*。

- **用户的登录事件，其凭据已泄露，导致 Office 365 模拟**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。 
    
    接下来，帐户 \<*帐户名称*> 在单个会话中 \<*数量的*> 不同帐户上模拟。

- **用户的登录事件，其凭据已泄露，导致批量文件共享**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。
    
    接下来，帐户 \<*帐户名*> 在单个会话中 \<>*的*唯一文件之间共享。

- **从已泄露凭据的用户登录到云应用中的勒索软件的登录事件**
    
    此警报指示 \<*帐户名*> 使用泄露凭据的登录事件。 
    
    接下来，帐户 \<*帐户名称*> 上传 \<> 文件*数*，并删除 \<*个*文件的总数。 
    
    此活动模式表示存在潜在的勒索软件攻击。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关高级多阶段攻击检测的详细信息，你可能会对以下快速入门感兴趣，以了解如何了解你的数据和潜在威胁： [Azure Sentinel 入门](quickstart-get-visibility.md)。

如果你已准备好调查为你创建的事件，请参阅以下教程：[通过 Azure Sentinel 调查事件](tutorial-investigate-cases.md)。

