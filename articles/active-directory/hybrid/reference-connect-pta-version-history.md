---
title: Azure AD 直通身份验证：版本发布历史记录 |微软文档
description: 本文列出了 Azure AD 直通身份验证代理的所有版本
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75b127f8429650d46af9f171ed7ff03692f1499e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379904"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD 直通身份验证代理：版本发布历史记录 
 
在本地安装的启用直通身份验证的代理会定期更新以提供新功能。 本文列出了引入新功能时添加的版本和功能。 发布新版本时，直通身份验证代理将自动更新。 

以下是相关主题： 

- [使用 Azure AD 直通身份验证的用户登录](how-to-connect-pta.md) 
- [Azure AD 直通身份验证代理安装](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>发布状态： 
04/09/2020： 发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

- 添加了在安装时定位云环境的支持。 可以将捆绑包固定到给定的云环境。



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>版本状态 
2019/1/22：发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对服务总线可靠通道的支持，为出站连接添加另一层连接恢复能力 
- 在代理注册期间强制执行 TLS 1.2 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>版本状态 
2018/4/10：发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Web 套接字连接支持 
- 将 TLS 1.2 设置为代理的默认协议 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>版本状态 
2018/1/31：发布供下载  
### <a name="fixed-issues"></a>修复的问题 

- 修复了导致代理中某些内存泄漏的 Bug。 
- 更新了 Azure 服务总线版本，其中包括连接器超时问题的错误修复程序。 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>版本状态 
2017/11/26：发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 添加了对代理和 Azure AD 服务之间基于 Websocket 的连接的支持，以提高连接恢复能力 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>版本状态 
2017/11/25：发布供下载  
### <a name="fixed-issues"></a>修复的问题 
- 修复了与默认代理方案的 DNS 缓存相关的 Bug 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>版本状态 
2017/10/17：发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- 为出站连接添加 DNS 缓存功能，以添加 DNS 故障的恢复能力 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>版本状态 
08/31/2017： 发布供下载  
### <a name="new-features-and-improvements"></a>新增功能和改进 
- Azure AD 直通身份验证代理的 GA 版本 

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 传递身份验证的用户登录](how-to-connect-pta.md)