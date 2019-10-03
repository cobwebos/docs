---
title: Azure AD Connect 预配代理：版本发布历史记录 | Microsoft Docs
description: 本文列出了 Azure AD Connect 预配代理的所有版本，并介绍了新功能和已修复的问题
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ce9549765f6a912b3e95f99d11da20347b82ad8
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326466"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect 预配代理：版本发行历史记录
本文列出了已发布 Azure Active Directory Connect 预配代理的版本和功能。 Azure AD 团队定期用新特性和功能更新设置代理。 发布新版本时，会自动更新设置代理。 

建议为代理启用 "自动更新"，以确保具有最新的功能和 bug 修复。 Microsoft 为最新的代理版本和之前的版本提供直接支持。

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>版本状态

2019年9月9日：已发布以进行自动更新

### <a name="new-features-and-improvements"></a>新增功能和改进

* 能够为调试预配代理问题配置其他跟踪和日志记录
* 只能提取在映射中配置为提高同步性能的 Azure AD 属性的功能

### <a name="fixed-issues"></a>修复的问题

* 修复了一个 bug，该 bug 在出现 Azure AD 连接失败的问题时，代理进入无响应状态
* 修复了从读取二进制数据时导致问题的 bug Azure Active Directory
* 修复了代理未能续订信任与云混合标识服务的 bug

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>版本状态

2019年1月23日：已发布，供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 改进预配代理和连接器体系结构，以获得更好的性能、稳定性和可靠性 
* 使用 UI 驱动的安装向导简化预配代理配置 
* 添加了对自动代理更新的支持

