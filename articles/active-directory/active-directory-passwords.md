<properties 
	pageTitle="什么是 Azure AD 密码管理 | Microsoft Azure"
	description="介绍 Azure AD 中的密码管理功能，包括密码重置、更改、密码管理报告，以及将密码写回到本地 Active Directory。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory"  
	ms.date="10/08/2015" 
	wacn.date=""/>

# 从任意位置管理密码

  >[AZURE.IMPORTANT]你是因为想要重置 Azure 或 O365 密码而来到这里吗？ 如果是，请[遵照此处的说明](https://support.microsoft.com/kb/2606983)。
  
利用自助服务降低成本和节省人力，一直以来都是世界各地 IT 部门追求的主要目标。事实上，市场中充斥着各种产品，让你能够从云或本地管理本地组、密码或用户配置文件。独树一帜的 Azure AD 提供一些现今市场上最容易使用且最强大的自助服务功能。

**Azure AD 密码管理**提供一组让用户随时随地从任何设备管理密码的功能，同时还能遵循你定义的安全策略。

## 概述
你在五分钟内即可开始体验 Azure AD 密码管理，而且在几小时内就能将它部署到整个组织。以下是一些有用资源，可帮助你使用该服务：

* [**工作原理**](/documentation/articles/active-directory-passwords-how-it-works) - 了解六个不同的服务组件及其功能
* [**入门**](/documentation/articles/active-directory-passwords-getting-started) - 了解如何让用户重置及更改云密码或本地密码
* [**自定义**](/documentation/articles/active-directory-passwords-customize) - 了解如何根据组织的需求自定义服务的外观和行为
* [**最佳实践**](/documentation/articles/active-directory-passwords-best-practices) - 了解如何快速部署且有效管理组织的密码
* [**深入分析**](/documentation/articles/active-directory-passwords-get-insights) - 了解集成式报告功能
* [**常见问题**](/documentation/articles/active-directory-passwords-faq) - 获取常见问题的解答
* [**故障排除**](/documentation/articles/active-directory-passwords-troubleshoot) - 了解如何快速排查服务的问题
* [**了解更多**](/documentation/articles/active-directory-passwords-learn-more) - 深入探索服务工作原理的技术细节


## Azure AD 密码管理有何用途？
以下是使用 Azure AD 密码管理功能可实现的某些目的。

- **自助密码更改**：可让最终用户或管理员就可以更改过期的或未过期的密码，而无需请求管理员或帮助台提供支持。
- **自助密码重置**：最终用户或管理员可以自行重置密码，而无需请求管理员或帮助台提供支持。自助密码重置功能需要 Azure AD 高级或基本版。有关详细信息，请参阅“Azure Active Directory 版本”。
- **管理员启动的密码重置**：管理员可以通过 [Azure 管理门户](https://manage.windowsazure.cn)重置某个最终用户的或其他管理员的密码。
- **密码管理活动报告**：管理员可以深入了解发生在其组织中的密码重置和注册活动。 
- **密码写回**：从云管理本地密码，因此，所有上述方案都可以由经过联合身份验证的或密码同步的用户本人或其代表来执行。密码写回功能需要 Azure AD Premium。有关详细信息，请参阅“Azure AD Premium 入门”。

## 为何使用 Azure AD 密码管理？
以下是应该使用 Azure AD 密码管理功能的某些原因

- **降低成本** - 支持人员辅助的密码重置通常占组织 IT 部门 20% 的费用
- **改善用户体验** - 用户不希望每次忘记密码时，都必须致电技术支持并花费大量时间在通话上
- **减少技术支持人力** - 密码管理是大部分组织设立技术支持的最重要因素
- **实现移动** - 用户可随时随地重置密码

## 最新服务更新

**注册页的可用性更新** - 2015 年 11 月

- 现在，在用户注册数据之后，他（她）只需单击“看起来不错”即可更新数据，而无需重新发送电子邮件或拨打电话。

**改进了密码写回的可靠性** - 2015 年 9 月

- 从 Azure AD Connect 九月版开始，密码写回代理现在将更积极地重试连接。另外，该版本还提供其他更稳健的故障转移功能。

**用于检索密码重置报告数据的 API** - 2015 年 8 月

- 现在，可以直接通过 [Azure AD 报告和事件 API](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 来检索密码重置报告幕后的数据。

**支持在云域加入期间执行 Azure AD 密码重置** - 2015 年 8 月

- 现在，任何云用户可以在云域加入登记体验过程中，直接从 Windows 10 登录屏幕重置其密码。请注意，此功能尚未在 Windows 10 登录屏幕上公开。

**在登录 Azure 和联合应用时强制密码重置注册** - 2015 年 7 月

- 除了在登录 myapps.microsoft.com 时强制注册以外，我们现在支持在登录 Azure 管理门户和任何联合单一登录应用程序期间强制注册

**安全问题本地化支持** - 2015 年 5 月

- 现在，你可以在配置密码重置的安全问题时，选择以完整 O365 语言集本地化的预定义安全问题。

**密码重置期间的帐户解锁支持** - 2015 年 6 月

- 如果你正在使用密码写回并在帐户已锁定时重置密码，我们将自动解锁你的 Active Directory 帐户！

**带品牌的 SSPR 注册** - 2015 年 4 月

- 密码重置注册页面现在已加上你的公司徽标！

**安全问题** - 2015 年 3 月

- 我们已将安全问题发布到 GA！

**帐户解锁** - 2015 年 3 月

- 用户现在可以在重置密码后解锁帐户

## 即将支持

下面是我们目前正在完善的一些优异功能！

**支持在不重置密码的情况下解锁 Active Directory 帐户** - 即将推出！

- 有很多人希望不重置密码就能解锁 AD 帐户。我们很高兴地宣布，此功能目前正处于扫尾阶段，即将向密码写回功能的所有用户推出！

**支持在登录期间提醒用户更新其注册的数据** - 正在开发

- 目前，我们支持在访问 myapps.microsoft.com 时提醒用户更新其注册的数据，但我们正努力实现对所有登录使用此功能。

**登录 Office 365 应用时强制密码重置注册** - 正在开发

- 有越来越多的办公应用正在融入最新、最完善的 Azure AD 登录体验。到时，这些应用将自动支持 SSPR 强制注册！

<br/> <br/> <br/>

**其他资源**


* [密码管理的工作原理](/documentation/articles/active-directory-passwords-how-it-works)
* [密码管理入门](/documentation/articles/active-directory-passwords-getting-started)
* [自定义密码管理](/documentation/articles/active-directory-passwords-customize)
* [密码管理最佳实践](/documentation/articles/active-directory-passwords-best-practices)
* [如何使用密码管理报告获取 Operational Insights](/documentation/articles/active-directory-passwords-get-insights)
* [密码管理常见问题](/documentation/articles/active-directory-passwords-faq)
* [排查密码管理问题](/documentation/articles/active-directory-passwords-troubleshoot)
* [了解详细信息](/documentation/articles/active-directory-passwords-learn-more)
* [MSDN 上的密码管理](https://msdn.microsoft.com/zh-cn/library/azure/dn510386.aspx) 

<!---HONumber=Mooncake_1221_2015-->