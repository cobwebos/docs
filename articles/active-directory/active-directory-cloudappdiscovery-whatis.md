---
title: "使用 Cloud App Discovery 查找非托管的云应用程序 | Microsoft Docs"
description: "提供有关如何使用 Cloud App Discovery 来查找和管理应用程序、这样做的好处以及其工作原理的信息。"
services: active-directory
keywords: "cloud app discovery, 管理应用程序"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 011cdf5f1e1b78832a8e4f18f4eef0f376860c45
ms.lasthandoff: 12/29/2016


---
# <a name="finding-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 查找非托管的云应用程序
## <a name="overview"></a>概述
在现代企业中，IT 部门不一定了解组织成员执行其工作所使用的所有云应用程序。 很容易知道为什么管理员对未经授权访问公司数据、可能的数据泄漏和其他安全风险有所顾虑。 缺乏认知可能使得要创建一个应付这些安全风险的计划让人却步。

Cloud App Discovery 是 Azure Active Directory (AD) Premium 的一项功能，可用于发现你组织中的人员所使用的云应用程序。

**使用 Cloud App Discovery，可以：**

* 查找正在使用的云应用程序，并根据用户数目、流量或应用程序的 Web 请求数目测量使用量。
* 识别正在使用应用程序的用户。
* 导出数据以进行离线分析。
* 使这些应用程序受 IT 控制并为用户管理启用单一登录。

## <a name="how-it-works"></a>工作原理
1. 应用程序使用代理安装在用户的计算机上。
2. 代理所捕获的应用程序使用情况信息会通过安全的加密通道发送到 Cloud App Discovery 服务。
3. Cloud App Discovery 服务会评估数据并生成报告。

![Cloud App Discovery 图示](./media/active-directory-cloudappdiscovery/cad01.png)

若要开始使用 Cloud App Discovery，请参阅 [Getting Started With Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)（Cloud App Discovery 入门）

## <a name="related-articles"></a>相关文章
* [Cloud App Discovery 的安全和隐私注意事项](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery Group Policy Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)（Cloud App Discovery 组策略部署指南）
* [Cloud App Discovery System Center Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)（Cloud App Discovery System Center 部署指南）
* [具有自定义端口的代理服务器的 Cloud App Discovery 注册表设置](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery Agent Changelog](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)（Cloud App Discovery 代理更改日志）
* [Cloud App Discovery Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)（Cloud App Discovery 常见问题）
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)


