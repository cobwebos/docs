---
title: Azure Sentinel 中的高级多阶段攻击检测
description: 使用 Azure Sentinel 中的合成技术来减少警报疲劳，并创建基于高级多阶段攻击检测的可操作事件。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: ba872f221f3bde29f0bb48b04dc2259d3ab4938a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906288"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 中的高级多阶段攻击检测


> [!IMPORTANT]
> Azure Sentinel 中的某些合成功能当前以 **公共预览版**提供。
> 提供这些功能没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

通过基于机器学习使用合成技术，Azure Sentinel 可以通过标识在终止链的各个阶段观察到的异常行为和可疑活动的组合来自动检测多阶段攻击。 根据这些发现，Azure Sentinel 会生成事件，否则可能难以捕获。 这些事件包含两个或多个警报或活动。 按照设计，这些事件是低容量、高保真和高严重性。

针对你的环境进行了自定义，此检测技术不仅可减少误报速率，还可以检测包含有限或缺失信息的攻击。

## <a name="configuration-for-advanced-multistage-attack-detection"></a>高级多阶段攻击检测的配置

默认情况下，Azure Sentinel 中启用此检测。 若要检查状态，或在使用替代解决方案来创建基于多个警报的事件时禁用状态，请使用以下说明：

1. 如果尚未这样做，请登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到**Azure Sentinel**  >  **配置**  >  **分析**

1. 选择 "**活动规则**"，然后通过筛选**合成**规则类型的列表，在 "**名称**" 列中找到 "**高级多阶段攻击检测**"。 检查 " **状态** " 列以确认是否已启用或禁用此检测。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}&quot;:::

1. 若要更改状态，请选择此项，然后在 &quot; **高级多阶段攻击检测** &quot; 边栏选项卡上，选择 &quot; **编辑**&quot;。

1. 在 **规则创建向导** 边栏选项卡上，会自动选择状态更改，因此请选择 &quot; **下一步：查看**&quot;，然后单击 &quot; **保存**&quot;。 

 由于 **合成** 规则类型仅包含一个不能修改的规则，因此规则模板不适用于此规则类型。

> [!NOTE]
> Azure Sentinel 目前使用30天的历史数据来训练机器学习系统。 此数据将在通过机器学习管道传递时使用 Microsoft 密钥进行加密。 但是，如果在 Azure Sentinel 工作区中启用了 CMK，则不会使用 [客户托管密钥 (CMK) ](customer-managed-keys.md) 来加密定型数据。 若要选择退出，请导航到**Azure Sentinel**   \>  **配置**   \>  **分析 \> 活动规则 \> 高级多阶段攻击检测**，然后在 **&quot;状态**" 列中，选择 "**禁用"。**

## <a name="attack-detection-scenarios"></a>攻击检测方案

以下部分列出了 Azure Sentinel 使用合成技术查找的相关方案的类型（按威胁分类分组）。

如上所述，由于合成关联各种产品的多个安全警报来检测高级多阶段攻击，因此成功的合成检测作为**合成事件**显示在 Azure Sentinel**事件**页上，而不是作为**日志**中**安全警报**表中的**警报**。

若要启用这些合成驱动的攻击检测方案，所列出的任何数据源都必须使用关联的 Azure Sentinel 数据连接器进行引入。

> [!NOTE]
> 其中一些方案为 **公共预览版**。 它们就是如此。

## <a name="compute-resource-abuse"></a>计算资源滥用

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>存在可疑 Azure Active Directory 登录后的多个 VM 创建活动
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问权限、影响 

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，资源劫持 (T1496) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在某个 Azure AD 帐户的可疑登录后，在单个会话中创建了异常数量的 Vm。 这种类型的警报表明，在跟踪事件说明中提到的帐户已泄露，并用于创建新的 Vm 进行未经授权的操作，例如运行加密挖掘操作。 具有多个 VM 创建活动警报的可疑 Azure AD 登录警报的排列方式如下：

- **不可能前往导致多个 VM 创建活动的非典型位置**

- **来自不熟悉的位置的登录事件，导致了多个 VM 创建活动**

- **来自受感染设备的登录事件，导致了多个 VM 创建活动**

- **从一个匿名 IP 地址登录到多个 VM 创建活动的登录事件**

- **用户的登录事件，其凭据已泄露，导致了多个 VM 创建活动**

## <a name="data-exfiltration"></a>数据外泄

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>在可疑 Azure AD 登录后，Office 365 邮箱渗透

**MITRE ATT&CK 战术：** 初始访问，渗透，集合

**MITRE ATT&CK 技术：** 有效帐户 (T1078) 、电子邮件集合 (T1114) 、自动渗透 (T1020) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在用户的收件箱 Azure AD 中出现可疑登录后，将出现可疑的收件箱转发规则。 这一指示使用户的帐户 (在合成事件说明) 已泄露，并通过启用邮箱转发规则（无需真实用户的知识）来盗取数据，从而使用户的帐户在已泄露。 与 Office 365 邮箱渗透警报的可疑 Azure AD 登录警报的排列方式如下：

- **不可能前往导致 Office 365 邮箱渗透的异常位置**

- **从指向 Office 365 邮箱渗透的不熟悉位置登录事件**

- **来自受感染设备的登录事件，导致 Office 365 邮箱渗透**

- **从通向 Office 365 邮箱渗透的匿名 IP 地址登录事件**

- **用户的登录事件，其凭据已泄露，导致 Office 365 邮箱渗透**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登录后的批量文件下载

**MITRE ATT&CK 战术：** 初始访问，渗透

**MITRE ATT&CK 技术：** 有效帐户 (T1078) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，用户在可疑登录到 Azure AD 帐户后下载了异常数量的文件。 这一指示使合成事件描述中所述的帐户受到破坏，并用于盗取组织网络中的数据。 带有大容量文件下载警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致大容量文件下载的异常位置**

- **从不熟悉的位置登录事件，导致批量文件下载**

- **来自受感染设备的登录事件，导致批量文件下载**

- **来自匿名 IP 的登录事件，导致批量文件下载**

- **用户的登录事件，其凭据已泄露，导致批量文件下载**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登录后的批量文件共享

**MITRE ATT&CK 战术：** 初始访问，渗透

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，渗透 Over Web Services (T1567) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，如果某个特定阈值超过特定阈值，则这些文件会在可疑登录到 Azure AD 帐户后共享给其他用户。 这一指示使合成事件说明中提到的帐户受到威胁，并用于通过共享文件（如文档、电子表格等）来盗取组织网络中的数据，并使未经授权的用户恶意使用。 具有大容量文件共享警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致大容量文件共享的异常位置**

- **从不熟悉的位置登录事件，导致批量文件共享**

- **来自受感染设备的登录事件，导致批量文件共享**

- **从导致批量文件共享的匿名 IP 地址登录事件**

- **用户的登录事件，其凭据已泄露，导致批量文件共享**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>可疑的收件箱操作规则在可疑 Azure AD 登录后设置
此方案属于此列表中的两个威胁分类： **数据渗透** 和 **横向移动**。 为清楚起见，它显示在两个部分中。

此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问、横向移动、渗透

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，内部鱼叉式网络钓鱼 (T1534) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在用户的收件箱中出现可疑登录后，会对 Azure AD 帐户设置异常收件箱规则。 这提供了一个很高的置信度表明，合成事件说明中提到的帐户已泄露，并用于出于恶意目的操作用户的电子邮件收件箱规则。 攻击者可能会尝试从组织的网络中盗取数据。 或者，攻击者可能会尝试从组织内部生成仿冒电子邮件， (绕过来自外部源的电子邮件的仿冒检测机制) 目的是通过获取对其他用户和/或特权帐户的访问权限来进行横向移动。 具有可疑收件箱操作规则警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致可疑收件箱操作规则的异常位置**

- **来自不熟悉的位置的登录事件导致了可疑的收件箱操作规则**

- **来自受感染设备的登录事件导致了可疑的收件箱操作规则**

- **来自匿名 IP 地址的登录事件，导致了可疑的收件箱操作规则**

- **用户的登录事件，其凭据已泄露，导致收到可疑收件箱操作规则**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Azure AD 登录时，多个 Power BI 报表共享活动 
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问，渗透 

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，渗透 Over Web Services (T1567) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在 Azure AD 帐户发生可疑登录后，在单个会话中共享了异常数量的 Power BI 报表。 这一指示使合成事件说明中提到的帐户受到破坏，并用于通过与未经授权的用户共享 Power BI 报表来盗取组织网络中的数据。 具有多个 Power BI 报表共享活动的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致多个 Power BI 报表共享活动的非典型位置**

- **来自不熟悉位置的登录事件，导致多个 Power BI 报表共享活动**

- **来自受感染设备的登录事件，导致了多个 Power BI 报表共享活动**

- **来自匿名 IP 地址的登录事件，该地址将导致多个 Power BI 报表共享活动**

- **来自泄露凭据的用户的登录事件，该事件将导致多个 Power BI 报表共享活动**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>可疑 Power BI 报表在以下可疑 Azure AD 登录时共享
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问，渗透 

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，渗透 Over Web Services (T1567) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在 Azure AD 帐户发生可疑登录后，会发生可疑 Power BI 报告共享活动。 共享活动被标识为可疑，因为 Power BI 报表包含使用自然语言处理标识的敏感信息，并且因为它是使用外部电子邮件地址（发布到 web）进行共享的，或者是作为快照传递到外部订阅电子邮件地址的。 此警报表明，在合成事件说明中提到的帐户已泄露，并已用于通过与未经授权的用户共享 Power BI 报表来盗取组织的敏感数据。 可疑的 Azure AD 登录警报与可疑 Power BI 报表共享的排列方式如下：  

- **不可能前往导致可疑 Power BI 报表共享的异常位置**

- **从不熟悉的位置登录事件，导致可疑 Power BI 报表共享**

- **来自受感染设备的登录事件，导致可疑 Power BI 报表共享**

- **来自匿名 IP 地址的登录事件，导致可疑 Power BI 报表共享**

- **用户的登录事件，该用户的凭据已泄露，导致可疑 Power BI 报表共享**

## <a name="data-destruction"></a>数据析构

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登录后发生批量文件删除

**MITRE ATT&CK 战术：** 初始访问权限、影响

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，Data 析构 (T1485) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在 Azure AD 帐户的可疑登录后删除了异常数量的唯一文件。 这表明，合成事件说明中提到的帐户可能已遭到破坏，并被用于销毁数据以用于恶意目的。 具有 "批量文件删除" 警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致批量文件删除的异常位置**

- **从不熟悉的位置登录事件，导致批量文件删除**

- **来自受感染设备的登录事件，导致批量文件删除**

- **从导致批量文件删除的匿名 IP 地址登录事件**

- **用户的登录事件，其凭据已泄露，导致批量文件删除**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>可疑 Azure AD 登录后发生可疑电子邮件删除活动
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问权限、影响 

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，Data 析构 (T1485) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在某个 Azure AD 帐户的可疑登录后，在单个会话中删除了异常数量的电子邮件。 这表明，合成事件说明中提到的帐户可能已泄露，并用于销毁数据以用于恶意目的，例如损害组织或隐藏与垃圾邮件相关的电子邮件活动。 可疑的 Azure AD 登录警报与可疑电子邮件删除活动警报的排列方式如下：   

- **不可能前往导致可疑电子邮件删除活动的异常位置**

- **从不熟悉的位置登录事件，导致电子邮件删除活动**

- **来自受感染设备的登录事件，导致可疑电子邮件删除活动**

- **来自匿名 IP 地址的登录事件，导致电子邮件删除活动**

- **用户的登录事件，该用户的凭据已泄露，导致电子邮件删除活动**

## <a name="denial-of-service"></a>拒绝服务

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>存在可疑 Azure AD 登录后的多个 VM 删除活动
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问权限、影响

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，Endpoint 拒绝服务 (T1499) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在某个 Azure AD 帐户的可疑登录后，在单个会话中删除了异常数量的 Vm。 这一指示使合成事件描述中所述的帐户受到安全威胁，并用于尝试破坏或破坏组织的云环境。 具有多个 VM 删除活动警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致多个 VM 删除活动的非典型位置**

- **从不熟悉的位置登录事件，导致多个 VM 删除活动**

- **来自受感染设备的登录事件，导致了多个 VM 删除活动**

- **从通向多个 VM 删除活动的匿名 IP 地址登录事件**

- **用户的登录事件，该用户的凭据已泄露，导致了多个 VM 删除活动**

## <a name="lateral-movement"></a>横向移动

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>在可疑 Azure AD 登录后，Office 365 模拟

**MITRE ATT&CK 战术：** 初始访问，横向移动

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，内部鱼叉式网络钓鱼 (T1534) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在 Azure AD 帐户的可疑登录后发生了异常数量的模拟操作。 在某些软件中，有允许用户模拟其他用户的选项。 例如，电子邮件服务允许用户授权其他用户代表他们发送电子邮件。 此警报表明，合成事件说明中所述的帐户已泄露，并用于出于恶意目的（例如发送用于恶意软件分发或横向移动的网页仿冒电子邮件）来执行模拟活动。 与 Office 365 模拟警报的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往导致 Office 365 模拟的异常位置**

- **从通向 Office 365 模拟的不熟悉的位置登录事件**

- **来自受感染设备的登录事件，导致 Office 365 模拟**

- **从通向 Office 365 模拟的匿名 IP 地址登录事件**

- **用户的登录事件，其凭据已泄露，导致 Office 365 模拟**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>可疑的收件箱操作规则在可疑 Azure AD 登录后设置
此方案属于此列表中的两个威胁分类： **横向移动** 和 **数据渗透**。 为清楚起见，它显示在两个部分中。

此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问、横向移动、渗透

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，内部鱼叉式网络钓鱼 (T1534) ，自动渗透 (T1020) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在用户的收件箱中出现可疑登录后，会对 Azure AD 帐户设置异常收件箱规则。 此指示使合成事件描述中所述的帐户受到安全威胁，并用于出于恶意目的操作用户的电子邮件收件箱规则。 攻击者可能会尝试从组织的网络中盗取数据。 或者，攻击者可能会尝试从组织内部生成仿冒电子邮件， (绕过来自外部源的电子邮件的仿冒检测机制) 目的是通过获取对其他用户和/或特权帐户的访问权限来进行横向移动。 具有可疑收件箱操作规则警报的可疑 Azure AD 登录警报的排列方式如下：

- **不可能前往导致可疑收件箱操作规则的异常位置**

- **来自不熟悉的位置的登录事件导致了可疑的收件箱操作规则**

- **来自受感染设备的登录事件导致了可疑的收件箱操作规则**

- **来自匿名 IP 地址的登录事件，导致了可疑的收件箱操作规则**

- **用户的登录事件，其凭据已泄露，导致收到可疑收件箱操作规则**

## <a name="malicious-administrative-activity"></a>恶意的管理活动

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>可疑的云应用管理活动关注可疑 Azure AD 登录

**MITRE ATT&CK 战术：** 初始访问、暂留、防御规避、横向移动、收集、渗透和影响

**MITRE ATT&CK 技术：** 不适用

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件表明，在同一帐户中出现可疑 Azure AD 登录后，在单个会话中执行了异常数量的管理活动。 这表明，合成事件说明中提到的帐户可能已泄露，并用于进行任何数量的未经授权的管理操作和恶意目的。 这也表示具有管理特权的帐户可能已泄露。 可疑的 Azure AD 登录警报与可疑的 cloud app 管理活动警报的排列方式如下：  

- **不可能前往导致可疑的云应用管理活动的异常位置**

- **来自不熟悉的位置的登录事件，导致云应用管理活动可疑**

- **来自受感染设备的登录事件，导致了可疑的云应用管理活动**

- **来自匿名 IP 地址的登录事件，导致云应用管理活动可疑**

- **用户的登录事件，该用户的凭据已泄露，导致云应用管理活动可疑**

## <a name="malicious-execution-with-legitimate-process"></a>具有合法进程的恶意执行

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell 发出了可疑的网络连接，然后是由 Palo Alto Networks 防火墙标记的异常流量。
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 操作

**MITRE ATT&CK 技术：** 命令和脚本解释器 (T1059) 

**数据连接器源：** Microsoft Defender for Endpoint (以前称为 Microsoft Defender 高级威胁防护，或 MDATP) ，Palo Alto 网络 

**说明：** 此类型的合成事件表明，通过 PowerShell 命令发出出站连接请求，并遵循 Palo Alto 网络防火墙检测到异常的入站活动。 这表明攻击者可能会获得网络访问权限，并试图执行恶意操作。 遵循此模式的 PowerShell 的连接尝试可能表示恶意软件命令和控制活动，请求下载其他恶意软件或攻击者建立远程交互式访问。 与所有 "居住于土地" 的攻击一样，此活动可能是 PowerShell 的合法使用。 但是，在执行 PowerShell 命令后，恶意的入站防火墙活动会提高以恶意方式使用 PowerShell 的信心，并应进一步进行调查。 在 Palo Alto 日志中，Azure Sentinel 侧重于 [威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，如果允许威胁 (可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires) ，则会将流量视为可疑。 另请参阅有关其他警报详细信息的合成事件描述中列出的 [威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威胁日志。

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>可疑远程 WMI 执行，之后是由 Palo Alto Networks 防火墙标记的异常流量
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 执行，发现

**MITRE ATT&CK 技术：** Windows Management Instrumentation (T1047) 

**数据连接器源：** Microsoft Defender for Endpoint (以前的 MDATP) ，Palo Alto 网络 

**说明：** 此类型的合成事件表明 (WMI) 命令在系统上远程执行的 Windows 管理接口，并遵循这种情况，Palo Alto 网络防火墙检测到可疑的入站活动。 这表明攻击者可能已获得对你网络的访问权限，并且正在尝试移动、提升权限和/或执行恶意负载。 与所有 "居住于土地" 的攻击一样，此活动可能是对 WMI 的合法使用。 但是，在执行远程 WMI 命令后，恶意的入站防火墙活动会提高 WMI 以恶意方式使用的可靠性，并且应该进一步进行调查。 在 Palo Alto 日志中，Azure Sentinel 侧重于 [威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，如果允许威胁 (可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires) ，则会将流量视为可疑。 另请参阅有关其他警报详细信息的合成事件描述中列出的 [威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威胁日志。

## <a name="malware-c2-or-download"></a>恶意软件 C2 或下载

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>TOR 匿名服务的网络请求，后跟 Palo Alto 网络防火墙标记的异常流量。
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 命令和控制

**MITRE ATT&CK 技术：** 加密通道 (T1573) ，Proxy (T1090) 

**数据连接器源：** Microsoft Defender for Endpoint (以前的 MDATP) ，Palo Alto 网络 

**说明：** 此类型的合成事件表明已向 TOR 匿名服务发出出站连接请求，随后 Palo Alto 网络防火墙检测到异常入站活动。 这表明攻击者可能会获得网络访问权限，并试图隐藏其操作和意向。 按照此模式连接到 TOR 网络可能表示恶意软件命令和控制活动，请求下载额外的恶意软件或攻击者建立远程交互式访问。 在 Palo Alto 日志中，Azure Sentinel 侧重于 [威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，如果允许威胁 (可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires) ，则会将流量视为可疑。 另请参阅有关其他警报详细信息的合成事件描述中列出的 [威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威胁日志。

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>到 IP 的出站连接，其中包含未经授权的访问尝试的历史记录，以及 Palo Alto 网络防火墙标记的异常流量
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 命令和控制

**MITRE ATT&CK 技术：** 不适用

**数据连接器源：** Microsoft Defender for Endpoint (以前的 MDATP) ，Palo Alto 网络 

**说明：** 此类型的合成事件表明，建立了与未经授权的访问尝试历史记录的 IP 地址的出站连接，并遵循 Palo Alto 网络防火墙检测到异常活动。 这表明攻击者可能会获得网络访问权限。 遵循此模式的连接尝试可能表示恶意软件命令和控制活动，请求下载其他恶意软件或攻击者建立远程交互式访问。 在 Palo Alto 日志中，Azure Sentinel 侧重于 [威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，如果允许威胁 (可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires) ，则会将流量视为可疑。 另请参阅有关其他警报详细信息的合成事件描述中列出的 [威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威胁日志。

## <a name="ransomware"></a>勒索软件

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>发生可疑 Azure AD 登录后的勒索软件执行

**MITRE ATT&CK 战术：** 初始访问权限、影响

**MITRE ATT&CK 技术：** 有效帐户 (T1078) ，对数据进行加密以 (T1486) 

**数据连接器源：** Microsoft Cloud App Security，Azure Active Directory Identity Protection

**说明：** 此类型的合成事件指示异常用户行为，指示在 Azure AD 帐户的可疑登录后检测到勒索软件攻击。 这一指示使合成事件说明中提到的帐户受到威胁，并用于对数据进行加密，以 extorting 数据所有者或拒绝数据所有者对其数据的访问。 与勒索软件执行警报发生的可疑 Azure AD 登录警报的排列方式如下：  

- **不可能前往通向云应用中的勒索软件的异常位置**

- **从不熟悉的位置登录事件，导致云应用中的勒索软件**

- **从受感染的设备登录到云应用中的勒索软件的登录事件**

- **从通向云应用中的勒索软件的匿名 IP 地址登录事件**

- **用户的登录事件，该用户的凭据已泄露到云应用中的勒索软件**

## <a name="remote-exploitation"></a>远程攻击

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>怀疑使用了攻击框架，并 Palo Alto 网络防火墙标记了异常流量
此方案目前为 **公共预览版**。

**MITRE ATT&CK 战术：** 初始访问、执行、横向移动、权限提升

**MITRE ATT&CK 技术：** 利用 Public-Facing 应用程序 (T1190) ，利用客户端执行 (T1203) ，利用远程服务 (T1210) ，利用权限提升 (T1068) 

**数据连接器源：** Microsoft Defender for Endpoint (以前的 MDATP) ，Palo Alto 网络 

**说明：** 此类型的合成事件表明，检测到与使用攻击框架（如 Metasploit）有关的非标准使用协议，并遵循 Palo Alto 网络防火墙检测到可疑的入站活动。 这可能是因为攻击者已利用服务来获取对你的网络资源的访问权限，或者攻击者已获取访问权限，并且正在尝试进一步利用可用的系统/服务来移动横向和/或提升的权限。 在 Palo Alto 日志中，Azure Sentinel 侧重于 [威胁日志](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)，如果允许威胁 (可疑数据、文件、洪水、数据包、扫描、间谍软件、url、病毒、漏洞、分秒必争、wildfires) ，则会将流量视为可疑。 另请参阅有关其他警报详细信息的合成事件描述中列出的 [威胁/内容类型](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) 的 Palo Alto 威胁日志。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关高级多阶段攻击检测的详细信息，你可能会对以下快速入门感兴趣，以了解如何了解你的数据和潜在威胁： [Azure Sentinel 入门](quickstart-get-visibility.md)。

如果你已准备好调查为你创建的事件，请参阅以下教程： [通过 Azure Sentinel 调查事件](tutorial-investigate-cases.md)。

