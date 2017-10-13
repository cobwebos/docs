---
title: "在 Azure Active Directory 中使用 Cloud App Discovery 查找非托管云应用程序 | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 查找非托管云应用程序
## <a name="summary"></a>摘要

Cloud App Discovery 是 Azure Active Directory Premium 的一项功能，可用于发现组织中的人员所使用的非托管云应用程序。 在现代企业中，IT 部门不一定了解组织成员执行其工作所使用的所有云应用程序。 很容易知道为什么管理员对未经授权访问公司数据、可能的数据泄漏和其他安全风险有所顾虑。 缺乏认知可能使得要创建一个应付这些安全风险的计划让人却步。

> [!TIP] 
> 在 Azure Active Directory (Azure AD) 中核查 Cloud App Discovery 的改进，该功能已通过[与 Microsoft Cloud App Security 集成](https://portal.cloudappsecurity.com)而得到加强。

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


## <a name="next-steps"></a>后续步骤
* [Cloud App Discovery 的安全和隐私注意事项](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [具有自定义端口的代理服务器的 Cloud App Discovery 注册表设置](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery Agent Changelog](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)（Cloud App Discovery 代理更改日志）
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

