<properties
	pageTitle="使用 Cloud App Discovery 查找非托管的云应用程序 | Azure"
	description="提供有关如何使用 Cloud App Discovery 来查找和管理应用程序、这样做的好处以及其工作原理的信息。"
	services="active-directory"
	keywords="cloud app discovery, 管理应用程序"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.date="04/30/2016"
	wacn.date=""/>

# 使用 Cloud App Discovery 查找非托管的云应用程序

## 概述
在现代企业中，IT 部门不一定了解组织成员执行其工作所使用的所有云应用程序。很容易知道为什么管理员对未经授权访问公司数据、可能的数据泄漏和其他安全风险有所顾虑。缺乏认知可能使得要创建一个应付这些安全风险的计划让人却步。

Cloud App Discovery 是 Azure Active Directory (AD) Premium 的一项功能，可用于发现你组织中的人员所使用的云应用程序。

**使用 Cloud App Discovery，你可以：**

- 查找正在使用的云应用程序，并根据用户数目、流量或应用程序的 Web 请求数目测量使用量。
- 识别正在使用应用程序的用户。
- 导出数据以进行离线分析。
- 使这些应用程序受 IT 控制并为用户管理启用单一登录。

## 工作原理
1. 应用程序使用代理安装在用户的计算机上。
2. 代理所捕获的应用程序使用情况信息会通过安全的加密通道发送到 Cloud App Discovery 服务。
3. Cloud App Discovery 服务会评估数据并生成报告。

![Cloud App Discovery 图示](./media/active-directory-cloudappdiscovery/cad01.png)

若要开始使用 Cloud App Discovery，请参阅 [Getting Started With Cloud App Discovery（Cloud App Discovery 入门）](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## 相关文章
- [Cloud App Discovery 的安全和隐私注意事项](/documentation/articles/active-directory-cloudappdiscovery-security-and-privacy-considerations)  
- [Cloud App Discovery Group Policy Deployment Guide（Cloud App Discovery 组策略部署指南）](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
- [Cloud App Discovery System Center Deployment Guide（Cloud App Discovery System Center 部署指南）](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
- [Cloud App Discovery Registry Settings for Proxy Servers with Custom Ports（具有自定义端口的代理服务器的 Cloud App Discovery 注册表设置）](/documentation/articles/active-directory-cloudappdiscovery-registry-settings-for-proxy-services)
- [Cloud App Discovery Agent Changelog（Cloud App Discovery 代理更改日志）](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
- [Cloud App Discovery Frequently Asked Questions（Cloud App Discovery 常见问题）](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
- [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)

<!---HONumber=Mooncake_0613_2016-->