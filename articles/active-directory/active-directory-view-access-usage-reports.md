<properties
	pageTitle="查看访问和使用情况报告 | Azure"
	description="说明如何查看访问和使用情况报告，以深入分析组织目录的完整性和安全性。"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="03/07/2016"
	wacn.date=""/>

# 查看访问和使用情况报告

本文档是 [Azure Active Directory 报告指南](/documentation/articles/active-directory-reporting-guide)的一部分。

你可以使用 Azure Active Directory 的访问和使用情况报告来监控你所在组织的目录的完整性和安全性。使用此信息，目录管理员可以更好地确定哪里可能存在安全风险，以便制定相应的计划来降低风险。

在 Azure 管理门户中，报告按以下方式分类：

- 异常报告 - 包含我们发现存在异常的登录事件。我们的目标是让你知道这项活动并使你能够就事件是否可疑做出决定。
- 集成应用程序报告 - 就你所在的组织如何使用云应用程序提供见解。Azure Active Directory 提供与数千个云应用程序的集成。
- 错误报告 - 指示在为外部应用程序设置帐户时可能发生的错误。
- 用户特定的报告 - 显示特定用户的设备/登录活动数据。
- 活动日志 - 包含过去 24 小时、过去 7 天或过去 30 天内的所有已审核事件的记录，以及组活动更改记录、密码重置和注册活动记录。

> [AZURE.NOTE]
>
- 仅当你已启用 [Azure Active Directory Premium](/documentation/articles/active-directory-get-started-premium) 时，一些高级异常报告和资源使用情况报告才可用。高级报告可帮助你提高访问安全性、应对潜在威胁以及获得对设备访问和应用程序使用情况进行分析。
- 在中国，使用 Azure Active Directory 全球实例的客户可以使用 Azure Active Directory 高级和基本版。由中国 21Vianet 运营的 Microsoft Azure 服务目前不支持 Azure Active Directory 高级和基本版。有关详细信息，请在 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

## 报告

|	报表 |	说明 |
|	------												|	-----																						|
|	**异常活动报告**
|	[来自未知源的登录](/documentation/articles/active-directory-reporting-sign-ins-from-unknown-sources) |	可能表示未受跟踪的登录尝试。 |
|	[多次失败后的登录](/documentation/articles/active-directory-reporting-sign-ins-after-multiple-failures) |	可能表示成功的暴力破解攻击。 |
|	[来自多个地理区域的登录](/documentation/articles/active-directory-reporting-sign-ins-from-multiple-geographies) |	可能表示多个用户正在使用同一帐户登录。 |
|	[从具有可疑活动的 IP 地址登录](/documentation/articles/active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity) |	可能表示在经过持续入侵尝试后成功登录。 |
|	[从可能受感染的设备登录](/documentation/articles/active-directory-reporting-sign-ins-from-possibly-infected-devices) |	可能表示从已受感染的设备尝试登录。 |
|	[异常登录活动](/documentation/articles/active-directory-reporting-irregular-sign-in-activity) |	可能表示对用户登录模式而言异常的事件。 |
|	[具有异常登录活动的用户](/documentation/articles/active-directory-reporting-users-with-anomalous-sign-in-activity) |	表示用户的帐户受到威胁。 |
|	具有已泄漏凭据的用户 |	具有已泄漏凭据的用户 |
|	**活动日志**
|	审核报告 |	目录中已审核的事件 |
|	密码重置活动 |	提供你组织中发生的密码重置活动的详细视图。 |
|	查看密码重置注册活动 |	提供你组织中发生的密码重置注册的详细视图。 |
|	自助服务组活动 |	提供目录中与组相关的所有活动的活动日志 |
|	**集成的应用程序**
|	应用程序使用情况 |	提供已集成到目录的所有 SaaS 应用程序的使用摘要。 |
|	帐户设置活动 |	提供向外部应用程序预配帐户的尝试的历史记录。 |
|	密码滚动更新状态 |	提供 SaaS 应用程序自动密码滚动更新状态的详细描述。 |
|	帐户设置错误 |	表示对用户访问外部应用程序造成的影响。 |
|	**Rights Management**
|	RMS 使用情况 |	提供 Rights Management 使用情况的摘要 |
|	最活跃的 RMS 用户 |	列出访问受 RMS 保护的文件的前 1000 个活跃用户 |
|	RMS 设备使用情况 |	列出用于访问受 RMS 保护的文件的设备 |
|	启用了 RMS 的应用程序使用情况 |	提供启用了 RMS 的应用程序的使用情况 |

## 报告版本

|	报表 |	免费 |	基本 |	高级 |
|	------												|	----	|	-----	|	--------	|
|	**异常活动报告**
|	来自未知源的登录 |	✓ |	✓ |	✓ |
|	多次失败后的登录 |	✓ |	✓ |	✓ |
|	来自多个地理区域的登录 |	✓ |	✓ |	✓ |
|	从具有可疑活动的 IP 地址登录 | | |	✓ |
|	从可能受感染的设备登录 | | |	✓ |
|	异常登录活动 | | |	✓ |
|	具有异常登录活动的用户 | | |	✓ |
|	具有已泄漏凭据的用户 | | |	✓ |
|	**活动日志**
|	审核报告 |	✓ |	✓ |	✓ |
|	密码重置活动 | | |	✓ |
|	查看密码重置注册活动 | | |	✓ |
|	自助服务组活动 | | |	✓ |
|	**集成的应用程序**
|	应用程序使用情况 | | |	✓ |
|	帐户设置活动 |	✓ |	✓ |	✓ |
|	密码滚动更新状态 | | |	✓ |
|	帐户设置错误 |	✓ |	✓ |	✓ |
|	**Rights Management**
|	RMS 使用情况 | | |	仅限 RMS |
|	最活跃的 RMS 用户 | | |	仅限 RMS |
|	RMS 设备使用情况 | | |	仅限 RMS |
|	启用了 RMS 的应用程序使用情况 | | |	仅限 RMS |



## 异常活动报告
<p>异常登录活动报告将会标识 Office365、Azure 管理门户、Azure AD 访问面板、Sharepoint Online、Dynamics CRM Online 和其他 Microsoft Online Services 中发生的可疑登录活动。</p>
<p>所有这些报告（“多次失败后的登录”报告除外）还会标识上述服务中发生的可疑<i>联合</i>登录，不管联合提供程序是什么。</p>
<p>可以使用以下报告：</p><ul>
<li>[来自未知源的登录](/documentation/articles/active-directory-reporting-sign-ins-from-unknown-sources)。</li>
<li>[多次失败后的登录](/documentation/articles/active-directory-reporting-sign-ins-after-multiple-failures)。</li>
<li>[来自多个地理位置的登录](/documentation/articles/active-directory-reporting-sign-ins-from-multiple-geographies)。</li>
<li>[来自具有可疑活动的 IP 地址的登录](/documentation/articles/active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity)。</li>
<li>[异常登录活动](/documentation/articles/active-directory-reporting-irregular-sign-in-activity)。</li>
<li>[来自可能受感染的设备的登录](/documentation/articles/active-directory-reporting-sign-ins-from-possibly-infected-devices)。</li>
<li>[具有异常登录活动的用户](/documentation/articles/active-directory-reporting-users-with-anomalous-sign-in-activity)。</li>
<li>具有已泄漏凭据的用户</li></ul>










## 活动日志

### 审核报告

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 显示在过去 24 小时、过去 7 天或过去 30 天内发生的所有已审核事件的记录。<br />有关详细信息，请参阅 [Azure Active Directory 审核报告事件](/documentation/articles/active-directory-reporting-audit-events) | “目录”>“报告”选项卡 |

![审核报告](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### 密码重置活动

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 显示你的组织中发生的所有密码重置尝试。 | “目录”>“报告”选项卡 |

![密码重置活动](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### 查看密码重置注册活动

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 显示你的组织中发生的所有密码重置注册 | “目录”>“报告”选项卡 |

![查看密码重置注册活动](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### 自助服务组活动

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 显示目录中自助管理组的所有活动。 | “目录”>“用户”>“用户”>“设备”选项卡 |

![自助服务组活动](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## 集成应用程序报告

### 应用程序使用情况：摘要

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 当你要查看目录中所有 SaaS 应用程序的使用情况时，可使用此报告。此报告基于用户在访问面板中单击应用程序的次数。 | “目录”>“报告”选项卡 |

此报告包含目录有权访问的所有应用程序（包括预先集成的 Microsoft 应用程序）的登录。

预先集成的 Microsoft 应用程序包括 Office 365、Sharepoint、Azure 管理门户，等等。

![应用程序使用情况摘要](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### 应用程序使用情况：详情

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 当你要查看特定 SaaS 应用程序的使用量时，可使用此报告。此报告基于用户在访问面板中单击应用程序的次数。 | “目录”>“报告”选项卡 |

### 应用程序仪表板

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 此报告指示在所选时间间隔内你所在组织的用户累积登录该应用程序的次数。面板页面上的图表将帮助你识别该应用程序全部使用情况的趋势。 | “目录”>“应用程序”>“仪表板”选项卡 |

## 错误报告

### 帐户设置错误

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 使用此报告监视在从 SaaS 应用程序将帐户同步到 Azure Active Directory 期间发生的错误。 | “目录”>“报告”选项卡 |

![帐户设置错误](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## 用户特定的报告

### 设备

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 当你想查看特定用户用于访问 Azure Active Directory 的设备的 IP 地址和地理位置时，可使用此报告。 | “目录”>“用户”>“用户”>“设备”选项卡 |

### 活动

| 说明 | 报告位置 |
| :-------------     | :-------        |
| 显示用户的登录活动。该报告包含登录的应用程序、所使用的设备、IP 地址和位置等信息。我们不收集使用 Microsoft 帐户登录的用户的历史记录。 | “目录”>“用户”>“用户”>“活动”选项卡 |

#### 用户活动报告中包含的登录事件

用户活动报告中只包括特定类型的登录事件。

| 事件类型 | 是否包括？ |
| ----------------------								| ---------		|
| 登录[访问面板](http://myapps.microsoft.com/) | 是 |
| 登录 [Azure 管理门户](https://manage.windowsazure.cn/) | 是 |
| 登录 [Microsoft Azure 门户](https://portal.azure.cn/) | 是 |
| 登录 [Office 365 门户](http://portal.office.com/) | 是 |
| 登录本机应用程序，例如 Outlook（参阅以下例外情况） | 是 |
| 通过访问面板登录联合/设置的应用程序，例如 Salesforce | 是 |
| 通过访问面板登录基于密码的应用程序，例如 Twitter | 是 |
| 登录已添加到目录中的自定义业务应用程序 | 否（即将包括） |
| 登录已添加到目录中的 Azure AD 应用程序代理应用 | 否（即将包括） |

> 注意：为了减少此报告中的干扰信息量，将不会显示 [Microsoft Online Services 登录助手](http://community.office365.com/zh-cn/w/sso/534.aspx)进行的登录。










## 当你怀疑有安全漏洞时要考虑的事项

如果你怀疑某个用户帐户可能已泄露或者出现可能导致云中的目录数据产生安全漏洞的任何可疑用户活动，你可能需要考虑采取以下一个或多个措施：

- 联系该用户以核实该活动
- 重置用户密码
- [启用多重身份验证](/documentation/articles/multi-factor-authentication-get-started)以提高安全性

## 查看或下载报告

1. 在 Azure 经典门户中，依次单击“Active Directory”、你所在组织的目录的名称和“报告”。
2. 在“报告”页面上，单击你要查看和/或下载的报告。

    > [AZURE.NOTE] 如果这是你第一次使用 Azure Active Directory 的报告功能，你将看到一条“选择加入”的消息。如果同意，请单击复选标记图标以继续。

3. 单击“间隔”旁边的下拉菜单，然后从以下时间范围选择其中一个作为生成此报告应使用的时间间隔：
    - 过去 24 小时
    - 过去 7 天
    - 过去 30 天
4. 单击复选标记图标以运行报告。
	- Azure 经典门户中可显示多达 1000 个事件。
5. 如果适用，请单击“下载”将报告下载到逗号分隔值 (CSV) 格式的压缩文件中，以便脱机查看或进行存档。
	- 下载的文件中最多包含 75,000 个事件。
	- 有关更多数据，请查看 [Azure AD Reporting API](/documentation/articles/active-directory-reporting-api-getting-started)。

## 忽略事件

如果你正在查看任何异常报告，则可能会注意到你可以忽略相关报告中出现的各种事件。若要忽略某个事件，只需在报告中突出显示该事件，然后单击“忽略”。“忽略”按钮会从报告中永久地删除突出显示的事件，但此按钮只能由获得许可的全局管理员使用。

## 自动电子邮件通知

有关 Azure AD 报告通知的详细信息，请查看 [Azure Active Directory 报告通知](/documentation/articles/active-directory-reporting-notifications)。

## 后续步骤

<!--- [Getting started with Azure Active Directory Premium](/documentation/articles/active-directory-get-started-premium)-->
- [向“登录”和“访问面板”页添加公司品牌](/documentation/articles/active-directory-add-company-branding)

<!---HONumber=Mooncake_0620_2016-->