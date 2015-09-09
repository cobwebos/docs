<properties 
	pageTitle="Azure Multi-Factor Authentication 报告" 
	description="介绍如何使用 Azure Multi-Factor Authentication 功能 - 报告。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.date="07/02/2015" 
	wacn.date=""/>

# Azure Multi-Factor Authentication 中的报告

Azure Multi-Factor Authentication 提供了几个报告供你和你的组织使用。可通过 Multi-Factor Authentication 管理门户访问这些报告。以下是可用报告的列表。

你可以通过 Azure 管理门户访问报告。

Name| 说明
:------------- | :------------- | 
使用情况 | 使用情况报告显示有关总体使用情况的信息、用户摘要和用户详细信息。
服务器状态|此报告显示与帐户关联的 Multi-Factor Authentication 服务器的状态。
阻止的用户历史记录|这些报告显示阻止或取消阻止用户的请求的历史记录。
跳过的用户历史记录|显示对用户的电话号码跳过 Multi-Factor Authentication 的请求的历史记录。
欺诈警报|显示在指定日期范围内提交的欺诈警报的历史记录。
已排队|列出排队等待处理的报告及其状态。报告完成后，将提供下载或查看报告的链接。

## 查看报告

1. 登录到 [http://www.windowsazure.cn](http://www.windowsazure.cn)
2. 在左侧选择“Active Directory”。
3. 在顶部选择“Multi-Factor Auth 提供程序”。此时将显示 Multi-Factor Auth 提供程序列表。
4. 如果你有多个 Multi-Factor Auth 提供程序，请选择要查看“欺诈警报”报告的 Multi-Factor Auth 提供程序，然后单击页底部的“管理”。如果你只有一个 Multi-Factor Auth 提供程序，则直接单击“管理”。此时将打开 Azure Multi-Factor Authentication 管理门户。
5. 在 Azure Multi-Factor Authentication 管理门户上，单击左侧的“查看报告”。在这里，你可以选择上述报告。


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**其他资源**

* [面向用户](/documentation/articles/multi-factor-authentication-end-user)
* [MSDN 上的 Azure Multi-Factor Authentication](https://msdn.microsoft.com/zh-cn/library/azure/dn249471.aspx)
 

<!---HONumber=69-->