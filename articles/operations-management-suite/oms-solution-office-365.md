---
title: Azure 中的 Office 365 管理解决方案 | Microsoft Docs
description: 本文详细介绍如何配置和使用 Azure 中的 Office 365 解决方案。  包括详细阐释 Log Analytics 中创建的 Office 365 记录。
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: 8797e08ad942687b7d2defd765f4fe3f9765812f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777842"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure 中的 Office 365 管理解决方案（预览）

![Office 365 徽标](media/oms-solution-office-365/icon.png)

通过 Office 365 管理解决方案，可在 Log Analytics 中监视 Office 365 环境。

- 监视 Office 365 帐户的用户活动，以分析使用模式和确定行为趋势。 例如，可提取特定使用方案，例如组织外共享的文件或最常用的 SharePoint 网站。
- 监视管理员活动，以跟踪配置更改或高特权操作。
- 检测并调查多余的用户行为，此操作可根据组织需求进行自定义。
- 演示审核和符合性。 例如，可监视对机密文件的文件访问操作，这对审核和符合性进程有所帮助。
- 通过对组织的 Office 365 活动数据使用[日志搜索](../log-analytics/log-analytics-log-search.md)，执行操作故障排除。

## <a name="prerequisites"></a>先决条件
需要以下各项才能安装和配置此解决方案。

- 组织的 Office 365 订阅。
- 充当全局管理员的用户帐户的凭据。
- 若要接收审核数据，必须在 Office 365 订阅中[配置审核](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)。  请注意，[邮箱审核](https://technet.microsoft.com/library/dn879651.aspx)单独配置。  若未配置审核，仍可安装解决方案和收集其他数据。
 


## <a name="management-packs"></a>管理包
此解决方案不会在连接的管理组中安装任何管理包。
  

## <a name="configuration"></a>配置
[向订阅添加 Office 365 解决方案](../log-analytics/log-analytics-add-solutions.md)后，必须将其连接到 Office 365 订阅。

1. 使用[“添加解决方案”](../log-analytics/log-analytics-add-solutions.md)中所述的流程，将警报管理解决方案添加到 Log Analytics 工作区。
2. 在 OMS 门户中，转到“设置”。
3. 在“连接的源”下，选择“Office 365”。
4. 单击“连接 Office 365”。<br>![连接 Office 365](media/oms-solution-office-365/configure.png)
5. 使用充当订阅的全局管理员的帐户登录 Office 365。 
6. 订阅将与解决方案要监视的工作负荷一并列出。<br>![连接 Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>数据收集
### <a name="supported-agents"></a>支持的代理
Office 365 解决方案不会从任何 [OMS 代理](../log-analytics/log-analytics-data-sources.md)中检索数据。  而直接从 Office 365 检索数据。

### <a name="collection-frequency"></a>收集频率
每次创建一条记录时，Office 365 都会向 Log Analytics 发送带详细数据的 [webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)。

## <a name="using-the-solution"></a>使用解决方案
向 Log Analytics 工作区添加 Office 365 解决方案时，“Office 365”磁贴将添加到你的仪表板。 此磁贴显示环境中计算机数量及其更新符合性的计数和图形表示形式。<br><br>
![Office 365 摘要磁贴](media/oms-solution-office-365/tile.png)  

单击“Office 365”磁贴，打开“Office 365”仪表板。

![Office 365 仪表板](media/oms-solution-office-365/dashboard.png)  

仪表板包含下表中的列。 每个列按照指定范围和时间范围内符合该列条件的计数列出了前十个警报。 可通过以下方式运行提供整个列表的日志搜索：单击该列底部的“全部查看”或单击列标题。

| 列 | 说明 |
|:--|:--|
| 操作 | 提供所有监视的 Office 365 订阅中的活动用户相关信息。 还能够看到随着时间的推移发生的活动数。
| Exchange | 显示 Exchange Server 活动的明细，例如 Add-Mailbox 权限或 Set-Mailbox。 |
| SharePoint | 显示用户在 SharePoint 文档上执行次数最多的一些活动。 从此磁贴向下钻取时，搜索页会显示这些活动的详细信息，例如目标文档和此活动的位置。 比如，对于文件访问事件，将能够看到正在访问的文档、其关联的帐户名以及 IP 地址。 |
| Azure Active Directory | 包含一些最活跃的用户活动，例如重置用户密码和登录尝试。 向下钻取时，将能够看到这些活动的详细信息（例如结果状态）。 如果想要监视 Azure Active Directory 上的可疑活动，这通常很有帮助。 |




## <a name="log-analytics-records"></a>Log Analytics 记录

Office 365 解决方案在 Log Analytics 工作区中创建的所有记录都具有 OfficeActivity 类型。  OfficeWorkload 属性确定记录所指的 Office 365 服务 - Exchange、AzureActiveDirectory、SharePoint 或 OneDrive。  RecordType 属性指定操作的类型。  每种操作类型的属性都不同，详情请见下表。

### <a name="common-properties"></a>通用属性
以下属性对于所有 Office 365 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| Type | OfficeActivity |
| ClientIP | 记录活动时使用的设备的 IP 地址。 IP 地址以 IPv4 或 IPv6 地址格式显示。 |
| OfficeWorkload | 记录所指的 Office 365 服务。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operation | 用户或管理员活动的名称。  |
| OrganizationId | 组织的 Office 365 租户的 GUID。 无论发生在哪种 Office 365 服务中，组织中的此值均保持不变。 |
| RecordType | 所执行操作的类型。 |
| ResultStatus | 指示操作（在 Operation 属性中指定）是成功还是失败。 可能的值有 Succeeded、PartiallySucceded 或 Failed。 对于 Exchange 管理员活动，值为 True 或 False。 |
| UserId | 执行使系统记下记录的操作的用户的 UPN（用户主体名称），例如 my_name@my_domain_name。 请注意，还包括系统帐户（例如 SHAREPOINT\system 或 NTAUTHORITY\SYSTEM）执行的活动的记录。 | 
| UserKey | UserId 属性中标识的用户的备用 ID。  例如，此属性由 SharePoint、OneDrive for Business 和 Exchange 中用户执行的事件的 Passport 唯一 ID (PUID) 进行填充。 此属性还可为其他服务中发生的事件以及系统帐户执行的事件指定与 UserID 属性相同的值|
| UserType | 执行操作的用户的类型。<br><br>管理员<br>Application<br>DcAdmin<br>常规<br>保留<br>服务主体<br>系统 |


### <a name="azure-active-directory-base"></a>Azure Active Directory Base
以下属性对于所有 Azure Active Directory 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 事件的类型。 |
| ExtendedProperties | Azure AD 事件的扩展属性。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 帐户登录
Active Directory 用户尝试登录时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Application | 触发帐户登录事件的应用程序，如 Office 15。 |
| Client | 有关客户端设备、设备操作系统和用于帐户登录事件的设备浏览器的详细信息。 |
| LoginStatus | 此属性直接从 OrgIdLogon.LoginStatus 获取。 可通过警报算法完成各种关注的登录失败的映射。 |
| UserDomain | 租户标识信息 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory
更改 Azure Active Directory 对象或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | 所执行操作（由 Operation 属性标识）针对的用户。 |
| Actor | 执行操作的用户或服务主体。 |
| ActorContextId | 参与者所属的组织的 GUID。 |
| ActorIpAddress | 采用 IPV4 或 IPV6 地址格式的参与者 IP 地址。 |
| InterSystemsId | 跨 Office 365 服务内的组件跟踪操作的 GUID。 |
| IntraSystemId |   由 Azure Active Directory 生成用于跟踪操作的 GUID。 |
| SupportTicketId | “代表执行”情况下的操作的客户支持票证 ID。 |
| TargetContextId | 目标用户所属的组织的 GUID。 |


### <a name="data-center-security"></a>数据中心安全
基于数据中心安全审核数据创建这些记录。  

| 属性 | 说明 |
|:--- |:--- |
| EffectiveOrganization | 提升/cmdlet 面向的租户的名称。 |
| ElevationApprovedTime | 提升获得批准时的时间戳。 |
| ElevationApprover | Microsoft 管理器的名称。 |
| ElevationDuration | 提升处于活动状态的持续时间。 |
| ElevationRequestId |  提升请求的唯一标识符。 |
| ElevationRole | 为其请求提升的角色。 |
| ElevationTime | 提升的开始时间。 |
| Start_Time | cmdlet 执行的开始时间。 |


### <a name="exchange-admin"></a>Exchange 管理员
更改 Exchange 配置时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  指定 cmdlet 是由组织中的用户运行、由 Microsoft 数据中心人员或数据中心服务帐户运行，还是由委派的管理员运行。 值 False 标识 cmdlet 由组织中的某人运行。 值 True 表示 cmdlet 由数据中心人员、数据中心服务帐户或委派的管理员运行。 |
| ModifiedObjectResolvedName |  这是由 cmdlet 修改的对象的用户友好名称。 仅在 cmdlet 修改对象时才记录此信息。 |
| OrganizationName | 租户的名称。 |
| OriginatingServer | 从中执行 cmdlet 的服务器的名称。 |
| parameters | 与 Operations 属性中标识的 cmdlet 结合使用的所有参数的名称和值。 |


### <a name="exchange-mailbox"></a>Exchange 邮箱
更改 Exchange 邮箱或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 用于执行操作的电子邮件客户端的相关信息，例如浏览器版本、Outlook 版本和移动设备信息。 |
| Client_IPAddress | 记录操作时所用的设备的 IP 地址。 IP 地址以 IPv4 或 IPv6 地址格式显示。 |
| ClientMachineName | 托管 Outlook 客户端的计算机名称。 |
| ClientProcessName | 用于访问邮箱的电子邮件客户端。 |
| ClientVersion | 电子邮件客户端的版本。 |
| InternalLogonType | 保留供内部使用。 |
| Logon_Type | 表示访问邮箱并执行所记录的操作的用户类型。 |
| LogonUserDisplayName |    执行操作的用户的用户友好名称。 |
| LogonUserSid | 执行操作的用户的 SID。 |
| MailboxGuid | 所访问邮箱的 Exchange GUID。 |
| MailboxOwnerMasterAccountSid | 邮箱所有者帐户的主帐户 SID。 |
| MailboxOwnerSid | 邮箱所有者的 SID。 |
| MailboxOwnerUPN | 拥有所访问邮箱的人员的电子邮件地址。 |


### <a name="exchange-mailbox-audit"></a>Exchange 邮箱审核
创建邮箱审核项时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | 表示对其执行操作的项 | 
| SendAsUserMailboxGuid | 为发送电子邮件而访问的邮箱的 Exchange GUID。 |
| SendAsUserSmtp | 被模拟用户的 SMTP 地址。 |
| SendonBehalfOfUserMailboxGuid | 为代替发送邮件而访问的邮箱的 Exchange GUID。 |
| SendOnBehalfOfUserSmtp | 以其名义发送电子邮件的用户的 SMTP 地址。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange 邮箱审核组
更改 Exchange 组或向其添加内容时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 组中每个项的相关信息。 |
| CrossMailboxOperations | 表示操作是否涉及多个邮箱。 |
| DestMailboxId | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定目标邮箱 GUID。 |
| DestMailboxOwnerMasterAccountSid | 仅在 CrossMailboxOperations 参数为 True 时设置。 为目标邮箱所有者的主帐户 SID 指定 SID。 |
| DestMailboxOwnerSid | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定目标邮箱的 SID。 |
| DestMailboxOwnerUPN | 仅在 CrossMailboxOperations 参数为 True 时设置。 指定的目标邮箱所有者的 UPN。 |
| DestFolder | 针对“移动”等操作的目标文件夹。 |
| Folder | 一组项所在的文件夹。 |
| Folders |     操作中涉及的源文件夹相关信息；例如如果文件夹已选中且随后删除。 |


### <a name="sharepoint-base"></a>SharePoint Base
这些属性对于所有 SharePoint 记录通用。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | 确定事件在 SharePoint 中发生。 可能的值有 SharePoint 或 ObjectModel。 |
| ItemType | 访问或修改的对象的类型。 请参阅 ItemType 表，详细了解对象类型。 |
| MachineDomainInfo | 有关设备同步操作的信息。 仅在此信息出现在请求中时，才对其报告。 |
| MachineId |   有关设备同步操作的信息。 仅在此信息出现在请求中时，才对其报告。 |
| Site_ | 用户访问的文件或文件夹所在的站点的 GUID。 |
| Source_Name | 触发已审核操作的实体。 可能的值有 SharePoint 或 ObjectModel。 |
| UserAgent | 用户客户端或浏览器的相关信息。 此信息由客户端或浏览器提供。 |


### <a name="sharepoint-schema"></a>SharePoint 架构
对 SharePoint 进行配置更改时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 自定义事件的可选字符串。 |
| Event_Data |  自定义事件的可选有效负载。 |
| ModifiedProperties | 包含此属性用于管理员事件，例如将用户添加为网站成员或网站集管理员组的成员。 该属性包括已修改的属性的名称（例如网站管理员组）、已修改属性的新值（例如已添加为网站管理员的用户），以及已修改的对象之前的值。 |


### <a name="sharepoint-file-operations"></a>SharePoint 文件操作
响应 SharePoint 中的文件操作时，将创建这些记录。

| 属性 | 说明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 复制或移动的文件的文件扩展名。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| DestinationFileName | 复制或移动的文件的名称。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| DestinationRelativeUrl | 复制或移动文件的目标文件夹的 URL。 SiteURL、DestinationRelativeURL 和 DestinationFileName 参数值的组合与 ObjectID 属性的值相同，即为已复制文件的完整路径名称。 此属性仅对 FileCopied 和 FileMoved 事件显示。 |
| SharingType | 分配给与其共享资源的用户的共享权限类型。 此用户由 UserSharedWith 参数标识。 |
| Site_Url | 用户访问的文件或文件夹所在的站点的 URL。 |
| SourceFileExtension | 用户访问的文件的文件扩展名。 如果所访问的对象是文件夹，则此属性空白。 |
| SourceFileName |  用户访问的文件或文件夹的名称。 |
| SourceRelativeUrl | 包含用户访问的文件的文件夹 URL。 SiteURL、SourceRelativeURL 和 SourceFileName 参数值的组合与 ObjectID 属性的值相同，即为用户访问的文件的完整路径名称。 |
| UserSharedWith |  与其共享资源的用户。 |




## <a name="sample-log-searches"></a>示例日志搜索
下表提供了此解决方案收集的更新记录的示例日志搜索。

| 查询 | 说明 |
| --- | --- |
|Office 365 订阅上所有操作的计数 |Type = OfficeActivity &#124; measure count() by Operation |
|SharePoint 网站的使用情况|Type=OfficeActivity OfficeWorkload=sharepoint &#124; measure count() as Count by SiteUrl &#124; sort Count asc|
|文件访问操作数（按用户类型）|Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed &#124; measure count() by UserType|
|使用特定关键字搜索|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|监视 Exchange 上的外部操作|Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true|



## <a name="troubleshooting"></a>故障排除

如果 Office 365 解决方案未按预期方式收集数据，请通过 OMS 门户中的“设置” -> “连接的源” -> “Office 365”检查其状态。 下表介绍了每种状态。

| 状态 | 说明 |
|:--|:--|
| 活动 | Office 365 订阅处于活动状态，且工作负荷已成功连接到 Log Analytics 工作区。 |
| 挂起的 | Office 365 订阅处于活动状态，但工作负荷尚未成功连接到 Log Analytics 工作区。 首次连接 Office 365 订阅时，所有工作负荷都将处于此状态直至成功连接。 请等待 24 小时，待所有工作负荷切换为“活动”。 |
| 非活动 | Office 365 订阅处于非活动状态。 请查看 Office 365 管理员页面了解详细信息。 激活 Office 365 订阅后，从 Log Analytics 工作区取消链接它，再重新链接以开始接收数据。 |



## <a name="next-steps"></a>后续步骤
* 使用[Log Analytics](../log-analytics/log-analytics-log-searches.md)中的日志搜索可查看详细的更新数据。
* [创建自己的仪表板](../log-analytics/log-analytics-dashboards.md)，显示最喜欢的 Office 365 搜索查询。
* [创建警报](../log-analytics/log-analytics-alerts.md)，主动接收重要的 Office 365 活动通知。  
