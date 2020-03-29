---
title: Azure AD 连接预配代理：版本发布历史记录 |微软文档
description: 本文列出了 Azure AD 连接配置代理的所有版本，并介绍了新功能和修复问题
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183238"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD 连接预配代理：版本发布历史记录
本文列出了已发布的 Azure 活动目录连接预配代理的版本和功能。 Azure AD 团队定期更新具有新功能和功能的预配代理。 预配代理在发布新版本时会自动更新。 

微软提供最新的代理版本和之前的一个版本的直接支持。

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>版本状态

2019 年 12 月 4 日：发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 包括支持[Azure AD 连接云配置](../cloud-provisioning/what-is-cloud-provisioning.md)，以同步用户、联系人和组数据（从本地活动目录到 Azure AD）


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>版本状态

2019 年 9 月 9 日：发布用于自动更新

### <a name="new-features-and-improvements"></a>新增功能和改进

* 能够配置其他跟踪和日志记录以调试预配代理问题
* 能够仅获取映射中配置的 Azure AD 属性，以提高同步性能

### <a name="fixed-issues"></a>修复的问题

* 修复了代理在 Azure AD 连接故障出现问题时进入无响应状态的 Bug
* 修复了从 Azure 活动目录中读取二进制数据时导致问题的错误
* 修复了代理未能与云混合标识服务续订信任的错误

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>版本状态

2019 年 1 月 23 日：发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 改进了配置代理和连接器架构，以更好的性能、稳定性和可靠性 
* 使用 UI 驱动的安装向导简化预配代理配置 
* 添加了对自动代理更新的支持

