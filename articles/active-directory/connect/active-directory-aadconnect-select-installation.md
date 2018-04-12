---
title: Azure AD Connect：选择安装类型 | Microsoft 文档
description: 本主题逐步讲解如何选择 Azure AD Connect 使用的安装类型
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 76f1ce12ab149f57ec6e995d132de83105c5e0ca
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>选择 Azure AD Connect 使用的安装类型
Azure AD Connect 为全新安装提供两种安装类型：快速安装和自定义安装。 本主题帮助用户确定安装期间要使用哪种选项。

## <a name="express"></a>Express
快速安装是最常用的选项，有 90% 的全新安装会使用它。 它能够为最常见的客户方案提供合适的配置。

该选项假设：

- 本地有单个 Active Directory 林。
- 有一个可用于安装的企业管理员帐户。
- 本地 Active Directory 中的对象不超过 100,000 个。

安装结果：

- 建立从本地到 Azure AD 的[密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)，实现单一登录。
- 可同步[用户、组、联系人和 Windows 10 计算机](active-directory-aadconnectsync-understanding-default-configuration.md)的配置。
- 同步所有域和所有 OU 中所有符合条件的对象。
- 启用[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)，确保始终使用最新的可用版本。

仍可使用“快速”选项的场合：

- 如果不想要同步所有 OU，仍可使用“快速”选项。请在最后一页上取消选择“启动同步过程...”。 然后再次运行安装向导，更改[配置选项](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options)中的 OU 并启用计划同步。
- 想要启用 Azure AD Premium 中的某个功能，例如密码写回。 首先请完成整个快速安装过程，以完成初始安装。 然后再次运行安装向导并更改[配置选项](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options)。

## <a name="custom"></a>“自定义”
自定义安装途径的选项要比快速安装多得多。 只要前一部分中所述的快速安装提供的配置无法满足组织的需要，就应该使用自定义安装。

使用时机：

- 无法访问 Active Directory 中的企业管理员帐户。
- 有多个林，或者计划将来要同步多个林。
- 无法通过 Connect 服务器访问林中的域。
- 计划对用户登录使用联合身份验证或传递身份验证。
- 对象超过 100,000 个，并且需要使用完整的 SQL Server。
- 计划使用基于组的筛选，而不仅是基于域或基于 OU 的筛选。

## <a name="upgrade-from-dirsync"></a>从 DirSync 升级
如果当前正在使用 DirSync，请遵循[从 DirSync 升级](active-directory-aadconnect-dirsync-upgrade-get-started.md)中的步骤升级现有配置。 有两个不同的升级选项：

- 就地升级：在同一台服务器上安装 Connect。
- 并行部署：在新服务器上安装 Connect，现有的 DirSync 服务器仍可正常运行。

## <a name="upgrade-from-azure-ad-sync"></a>从 Azure AD Sync 升级
如果当前正在使用 Azure AD Sync，可以遵循从一个 Connect 版本升级到更新版本时采用的[相同步骤](active-directory-aadconnect-upgrade-previous-version.md)。 有两个不同的升级选项：

- 就地升级：在同一台服务器上安装 Connect。
- 交叉迁移：在新服务器上安装 Connect，现有的 Azure AD Sync 服务器仍可正常运行。

## <a name="migrate-from-fim2010-or-mim2016"></a>从 FIM2010 或 MIM2016 迁移
如果当前正在使用装有 Azure AD 连接器的 Forefront Identity Manager 2010 或 Microsoft Identity Manager 2016，则唯一可用的选项就是迁移。 请遵循[交叉迁移](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)中所述的步骤。 执行这些步骤时，请将出现的所有 Azure AD Sync 替换为 FIM2010/MIM2016。

## <a name="next-steps"></a>后续步骤
根据选择使用的选项，请参考左侧的目录查找包含详细步骤的文章。
