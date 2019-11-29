---
title: Azure AD 传递身份验证：版本发布历史记录 |Microsoft Docs
description: 本文列出了 Azure AD 直通身份验证代理的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78293845e727d658a7d3b239c0ad14a47528cf2
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559363"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 传递身份验证代理：版本发布历史记录 
 
本地安装的用于启用直通身份验证的代理会定期更新，以提供新的功能。 本文列出了在引入新功能时添加的版本和功能。 发布新版本时，将自动更新直通身份验证代理。 

下面是相关主题： 

- [使用 Azure AD 传递身份验证的用户登录](how-to-connect-pta.md) 
- [Azure AD 直通身份验证代理安装](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>版本状态 
1/22/2019：已发布以供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对服务总线可靠通道的支持，以便为出站连接添加另一层连接复原 
- 在代理注册过程中强制执行 TLS 1。2 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>版本状态 
4/10/2018：已发布以供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Web 套接字连接支持 
- 将 TLS 1.2 设置为代理的默认协议 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>版本状态 
1/31/2018：已发布以供下载  
### <a name="fixed-issues"></a>修复的问题 

- 修复了一个 bug，该 bug 导致代理中的内存泄漏。 
- 更新了 Azure 服务总线版本，其中包括针对连接器超时问题的 bug 修复。 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>版本状态 
11/26/2017：已发布以供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对代理和 Azure AD 服务之间基于 websocket 的连接的支持，以提高连接复原能力 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>版本状态 
11/25/2017：已发布以供下载  
### <a name="fixed-issues"></a>修复的问题 
- 修复了与默认代理方案的 DNS 缓存相关的 bug 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>版本状态 
10/17/2017：已发布以供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 为出站连接添加了 DNS 缓存功能，以添加来自 DNS 故障的复原能力 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>版本状态 
08/31/2017：已发布以供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Azure AD 直通身份验证代理的 GA 版本 

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 直通身份验证的用户登录](how-to-connect-pta.md)