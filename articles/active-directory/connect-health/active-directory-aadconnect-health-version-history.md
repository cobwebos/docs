---
title: Azure AD Connect Health 版本历史记录
description: 本文档介绍 Azure AD Connect Health 的版本及其包含的功能。
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 7147b282d9bcbf48391d60466c77d28eca396832
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294269"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health：版本发布历史记录
Azure Active Directory 团队会定期更新 Azure AD Connect Health 的新特性和功能。 本文列出已发布的版本和功能。

## <a name="june-2018"></a>2018 年 6 月 
**新的预览功能：** 
* 用于同步的 Azure AD Connect Health - 从门户诊断并修正重复的属性同步错误 

**代理更新：** 
*   适用于 AD FS 的 Azure AD Connect Health 代理（3.1.2.0 版）  
   1. 警报的 Bug 修复和改进 


## <a name="may-2018"></a>2018 年 5 月
**代理更新：**
*   适用于 AD DS 的 Azure AD Connect Health 代理（3.0.244.0 版）
 1. 代理隐私改进  
 2. 修复了 Bug，进行了一般改进

*   适用于 AD FS 的 Azure AD Connect Health 代理（3.0.244.0 版）
 1. 代理诊断服务和相关的 PowerShell 模块改进
 2. 代理隐私改进  
 3. 修复了 Bug，进行了一般改进

* 适用于同步的 Azure AD Connect Health（3.0.164.0 版），与 Azure AD Connect 1.1.819.0 版一起发布
 1. 代理隐私改进  
 2. 修复了 Bug，进行了一般改进


## <a name="march-2018"></a>2018 年 3 月
**新的预览功能：**
* 适用于 AD FS 的 Azure Active Directory Connect Health - 有风险的 IP 报表和警报。

**代理更新：**

*   适用于 AD DS 的 Azure AD Connect Health 代理（3.0.176.0 版）
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进
*   适用于 AD FS 的 Azure AD Connect Health 代理（3.0.176.0 版）
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进
* 适用于 Sync 的 Azure AD Connect Health 代理（3.0.176.0 版）
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进

## <a name="december-2017"></a>2017 年 12 月
**代理更新：**

*   适用于 AD DS 的 Azure AD Connect Health 代理（3.0.145.0 版）
  1. 提高了代理可用性 
  2. 新增了代理故障排除命令
  3. 修复了 Bug，进行了一般改进
*   适用于 AD FS 的 Azure AD Connect Health 代理（3.0.145.0 版）
  1. 新增了代理故障排除命令
  2. 提高了代理可用性 
  3. 修复了 Bug，进行了一般改进
  
## <a name="october-2017"></a>2017 年 10 月
**代理更新：**

 * 适用于同步的 Azure AD Connect Health 代理（3.0.129.0 版），与 Azure AD Connect 1.1.649.0 版一起发布
<br></br> 修复了 Azure AD Connect 与适用于同步的 Azure AD Connect Health 代理之间存在的版本兼容性问题。此问题会影响要执行 Azure AD Connect 就地升级到版本 1.1.647.0，但当前 Health 代理版本为 3.0.127.0 的用户。 升级之后，Health 代理不再能够将有关 Azure AD Connect 同步服务的运行状况数据发送到 Azure AD Health 服务。 通过此修复，在 Azure AD Connect 就地升级过程中将安装 Health 代理版本 3.0.129.0。 Health 代理版本 3.0.129.0 与 Azure AD Connect 版本 1.1.649.0 没有兼容性问题。

## <a name="july-2017"></a>2017 年 7 月
**代理更新：**

*   适用于 AD DS 的 Azure AD Connect Health 代理（3.0.68.0 版）
  1. 修复了 Bug，进行了一般改进
  2. 支持 Sovereign 云
*   适用于 AD FS 的 Azure AD Connect Health 代理（3.0.68.0 版）
  1. 修复了 Bug，进行了一般改进
  2. 支持 Sovereign 云
* 适用于同步的 Azure AD Connect Health（3.0.68.0 版），与 Azure AD Connect 1.1.614.0 版一起发布
  1. 支持 Microsoft Azure 政府云和 Microsoft 德国云

## <a name="april-2017"></a>2017 年 4 月      
**代理更新：**

*   适用于 AD FS 的 Azure AD Connect Health 代理（3.0.12.0 版）
  1. 修复了 Bug，进行了一般改进
*   适用于 AD DS 的 Azure AD Connect Health 代理（3.0.12.0 版）
  1. 改进了性能计数器上传
  2. 修复了 Bug，进行了一般改进

## <a name="october-2016"></a>2016 年 10 月
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（2.6.408.0 版）
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
* 在适用于 AD FS 的 Azure AD Connect Health 代理中改进了连接和数据上传。

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

