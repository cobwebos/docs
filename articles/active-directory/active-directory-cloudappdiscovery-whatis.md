---
title: "在 Azure Active Directory 中使用 Cloud App Discovery 查找非托管云应用程序 | Microsoft Docs"
description: "提供有关如何使用 Cloud App Discovery 来查找和管理应用程序、这样做的好处以及其工作原理的信息。"
services: active-directory
keywords: "cloud app discovery, 管理应用程序"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>使用 Cloud App Discovery 查找非托管云应用程序
## <a name="summary"></a>摘要

Azure Active Directory 中的 Cloud App Discovery 现在由 Microsoft Cloud App Security 支持，提供增强的无代理发现体验。 若要使用 Cloud App Discovery，只需使用 Azure AD Premium P1 凭据登录。 所有 Azure AD Premium P1 客户均可获得此更新，无需额外付费。 首先阅读[在 Azure AD 中设置 Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started) 一文，然后试用 [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/)。

> [!IMPORTANT] 
> 当前的 Azure AD Cloud App Discovery 提供基于代理的发现体验，将于 2018 年 3 月 5 日关闭，之后会禁用代理和删除数据。 请在 3 月 5 日之前采取行动，启动并运行新的体验，避免服务中断。  
 
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

