---
title: Azure AD Connect Health 版本历史记录
description: 本文档介绍 Azure AD Connect Health 的版本及其包含的功能。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58de8de9a9fab67d743d4560ccda037d8f41301b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298987"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health：版本发行历史记录
Azure Active Directory 团队会定期更新 Azure AD Connect Health 的新特性和功能。 本文列出已发布的版本和功能。  

> [!NOTE]
> 连接发布新版本时，将自动更新代理的运行状况。 请确保从 Azure 门户启用自动升级设置。 
>

适用于同步的 Azure AD Connect Health 与 Azure AD Connect 安装相集成。 详细了解 [Azure AD Connect 版本历史记录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)有关功能反馈，请在 [Connect Health User Voice 频道](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)进行投票


## <a name="may-2019"></a>2019 年 5 月
**代理更新：** 
* 适用于 AD FS （版本 3.1.51.0） 的 azure AD Connect Health 代理 
   1. Bug 修复，用于区分多个登录共享同一个客户端请求 id。
   2. Bug 修复，可分析不当的用户名/密码错误语言本地化服务器。   

## <a name="april-2019"></a>2019 年 4 月
**代理更新：** 
* 适用于 AD FS （版本 3.1.46.0） 的 azure AD Connect Health 代理 
   1. 修复检查重复的 SPN 警报过程的 ADFS

## <a name="march-2019"></a>2019 年 3 月
**代理更新：** 
* 适用于 AD DS （版本 3.1.41.0） 的 azure AD Connect Health 代理  
   1. .NET 版本集合
   2. 改进的性能计数器收集时缺少某些类别
   3. 阻止生成的多个监视代理实例上的 bug 修复

* 适用于 AD FS （版本 3.1.41.0） 的 azure AD Connect Health 代理 
   1. 集成并升级的 AD FS 测试脚本使用 ADFSToolBox
   2. 实现.NET 版本的集合
   3. 改进的性能计数器收集时缺少某些类别
   4. 阻止生成的多个监视代理实例上的 bug 修复


## <a name="november-2018"></a>2018 年 11 月
**正式版新功能：** 
* 用于同步的 Azure AD Connect Health - 从门户诊断并修正重复的属性同步错误

**代理更新：** 
* 适用于 AD DS 的 Azure AD Connect Health 代理（3.1.24.0 版） 
   1. 传输层安全性 (TLS) 协议版本 1.2 符合性和强制执行
   2. 减少全局编录警报噪音
   3. 运行状况代理注册 bug 修复

* 适用于 AD FS 的 Azure AD Connect Health 代理（3.1.24.0 版）  
   1. 传输层安全性 (TLS) 协议版本 1.2 符合性和强制执行
   2. Test-ADFSRequestToken 对本地化操作系统的支持
   3. 解决了诊断代理事件处理程序锁定问题
   4. 运行状况代理注册 bug 修复

## <a name="august-2018"></a>2018 年 8 月 
*  适用于同步的 Azure AD Connect Health（3.1.7.0 版），与 Azure AD Connect 1.1.880.0 版一起发布    
   1. [使用 .NET Framework KB 版本监视代理的高 CPU 问题](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)的修补程序

## <a name="june-2018"></a>2018 年 6 月 
**新的预览功能：** 
* 用于同步的 Azure AD Connect Health - 从门户诊断并修正重复的属性同步错误 

**代理更新：** 
* 适用于 AD DS 的 Azure AD Connect Health 代理（3.1.7.0 版）    
  1. [使用 .NET Framework KB 版本监视代理的高 CPU 问题](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)的修补程序
   
* 适用于 AD FS 的 Azure AD Connect Health 代理（3.1.7.0 版）  
  1. [使用 .NET Framework KB 版本监视代理的高 CPU 问题](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)的修补程序
  2. 测试结果在 ADFS Server 2016 辅助服务器上修复
   
* 适用于 AD FS 的 Azure AD Connect Health 代理（3.1.2.0 版）  
  1. 代理内存管理和专用于版本 3.0.244.0 的相关警报修补程序


## <a name="may-2018"></a>2018 年 5 月
**代理更新：**
* 适用于 AD DS 的 Azure AD Connect Health 代理（3.0.244.0 版）
  1. 代理隐私改进  
  2. 修复了 Bug，进行了一般改进

* 适用于 AD FS 的 Azure AD Connect Health 代理（3.0.244.0 版）
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

* 适用于 AD DS 的 Azure AD Connect Health 代理（3.0.176.0 版）
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进
* 适用于 AD FS 的 Azure AD Connect Health 代理（3.0.176.0 版）
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进
* 适用于同步的 Azure AD Connect Health（3.0.129.0 版），与 Azure AD Connect 1.1.750.0 版一起发布  
  1. 提高了代理可用性 
  2. 修复了 Bug，进行了一般改进

## <a name="december-2017"></a>2017 年 12 月
**代理更新：**

* 适用于 AD DS 的 Azure AD Connect Health 代理（3.0.145.0 版）
  1. 提高了代理可用性 
  2. 新增了代理故障排除命令
  3. 修复了 Bug，进行了一般改进
* 适用于 AD FS 的 Azure AD Connect Health 代理（3.0.145.0 版）
  1. 新增了代理故障排除命令
  2. 提高了代理可用性 
  3. 修复了 Bug，进行了一般改进
  
## <a name="october-2017"></a>2017 年 10 月
**代理更新：**

 * 适用于同步的 Azure AD Connect Health 代理（3.0.129.0 版），与 Azure AD Connect 1.1.649.0 版一起发布
<br></br> 修复了 Azure AD Connect 与适用于同步的 Azure AD Connect Health 代理之间存在的版本兼容性问题。此问题会影响要执行 Azure AD Connect 就地升级到版本 1.1.647.0，但当前 Health 代理版本为 3.0.127.0 的用户。 升级之后，Health 代理不再能够将有关 Azure AD Connect 同步服务的运行状况数据发送到 Azure AD Health 服务。 通过此修复，在 Azure AD Connect 就地升级过程中将安装 Health 代理版本 3.0.129.0。 Health 代理版本 3.0.129.0 与 Azure AD Connect 版本 1.1.649.0 没有兼容性问题。

## <a name="july-2017"></a>2017 年 7 月
**代理更新：**

* 适用于 AD DS 的 Azure AD Connect Health 代理（3.0.68.0 版）
  1. 修复了 Bug，进行了一般改进
  2. 支持 Sovereign 云
* 适用于 AD FS 的 Azure AD Connect Health 代理（3.0.68.0 版）
  1. 修复了 Bug，进行了一般改进
  2. 支持 Sovereign 云
* 适用于同步的 Azure AD Connect Health（3.0.68.0 版），与 Azure AD Connect 1.1.614.0 版一起发布
  1. 支持 Microsoft Azure 政府云和 Microsoft 德国云

## <a name="april-2017"></a>2017 年 4 月      
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（3.0.12.0 版）
  1. 修复了 Bug，进行了一般改进
* 适用于 AD DS 的 Azure AD Connect Health 代理（3.0.12.0 版）
  1. 改进了性能计数器上传
  2. 修复了 Bug，进行了一般改进

## <a name="october-2016"></a>2016 年 10 月
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（2.6.408.0 版）
* 改进在身份验证请求中检测客户端 IP 地址的方法
* 与警报相关的 Bug 修复
* 适用于 AD DS 的 Azure AD Connect Health 代理（2.6.408.0 版）
* 与警报相关的 Bug 修复。
* 用于同步的 Azure AD Connect Health（2.6.353.0 版），与 Azure AD Connect 1.1.281.0 版一起发布
* 为同步错误报告提供所需的数据
* 与警报相关的 Bug 修复

**新的预览功能：**

* Azure AD Connect 的同步错误报告

**新功能：**

* 适用于 AD FS 的 Azure AD Connect Health - 在有关前 50 名存在用户名/密码错误的用户的报告中，提供了 IP 地址字段。

## <a name="july-2016"></a>2016 年 7 月
**新的预览功能：**

* [用于 AD DS 的 Azure AD Connect Health](how-to-connect-health-adds.md)。

## <a name="january-2016"></a>2016 年 1 月
**代理更新：**

* 适用于 AD FS 的 Azure AD Connect Health 代理（2.6.91.1512 版）

**新功能：**

* [Azure AD Connect Health 代理的测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 年 11 月
**新功能：**

* 支持[基于角色的访问控制](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**新的预览功能：**

* [用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)。

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
有关详细信息，请参阅[在云中监视本地标识基础结构和同步服务](whatis-hybrid-identity-health.md)。

