---
title: "Azure AD Connect Health 版本历史记录"
description: "本文档介绍 Azure AD Connect Health 的版本及其包含的功能。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2daa6c07191da7bd732cadf62b414f8027682071
ms.openlocfilehash: 35d7ac416c35c74d38f4370ee7e34a96eb18d000


---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health：版本发布历史记录
Azure Active Directory 团队会定期更新 Azure AD Connect Health 的新特性和功能。 本文列出已发布的版本和功能。

## <a name="october-2016"></a>2016 年 10 月
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（\(2.6.408.0 版\)）
  1. 改进在身份验证请求中检测客户端 IP 地址的方法
  2. 与警报相关的 Bug 修复
* 适用于 AD DS 的 Azure AD Connect Health 代理（2.6.408.0 版）
  1. 与警报相关的 Bug 修复。
* 用于同步的 Azure AD Connect Health（2.6.353.0 版），与 Azure AD Connect 1.1.281.0 版一起发布
  1. 为同步错误报告提供所需的数据
  2. 与警报相关的 Bug 修复

**新的预览功能：**

* Azure AD Connect 的同步错误报告

**新功能：**

* 适用于 AD FS 的 Azure AD Connect Health - 在有关前 50 名存在用户名/密码错误的用户的报告中，提供了 IP 地址字段。

## <a name="july-2016"></a>2016 年 7 月
**新的预览功能：**

* [用于 AD DS 的 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)。

## <a name="january-2016"></a>2016 年 1 月
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（2.6.91.1512 版）

**新功能：**

* [Azure AD Connect Health 代理的测试连接工具](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 年 11 月
**新功能：**

* 支持[基于角色的访问控制](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**新的预览功能：**

* [用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)。

**已解决的问题：**

* 针对代理注册期间出现的错误进行 Bug 修复。

## <a name="september-2015"></a>2015 年 9 月
**新功能：**

* AD FS 的错误用户名密码报告
* 支持配置未经身份验证的 HTTP 代理
* 支持在服务器核心上配置代理
* 改进了 AD FS 的警报
* 在适用于 AD FS 的 Azure AD Connect Health 代理中改进了连接和数据上载。

**已解决的问题：**

* 对 AD FS 使用洞察信息错误类型的 Bug 修复。

## <a name="june-2015"></a>2015 年 6 月
**适用于 AD FS 和 AD FS 代理的 Azure AD Connect Health 初始发行。**

**新功能：**

* 监视 AD FS 与 AD FS 代理服务器时以电子邮件通知发出警报。
* 在 AD FS 性能计数器中轻松访问 AD FS 拓扑和模式。
* 显示 AD FS 服务器上成功令牌请求的趋势，信息已按应用程序、身份验证方法、请求网络位置等类别分组。
* 显示 AD FS 服务器上失败请求的趋势，信息已按应用程序、错误类型等类别分组。
* 使用 Azure AD 全局管理员凭据简化代理部署。  

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅[在云中监视本地标识基础结构和同步服务](active-directory-aadconnect-health.md)。




<!--HONumber=Feb17_HO1-->


