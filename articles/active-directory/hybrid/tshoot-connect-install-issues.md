---
title: 排查 Azure AD Connect 安装问题 | Microsoft Docs
description: 本主题提供有关如何排查 Azure AD Connect 安装问题的步骤。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 56fd4be85fabebcdde5ca271630e1c5b83c398b9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771769"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>故障排除：Azure AD Connect 安装问题

## <a name="recommended-steps"></a>**建议的步骤**
请查看适合你的 [Azure AD Connect 安装类型](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)。 如果满足快速安装的条件，则强烈建议进行快速安装。 快速安装提供完成安装所需的最少选项，因此出现问题的可能性更小。 

但是，如果不满足快速安装的条件，并且必须执行自定义安装，则下文提供一些可用于避免常见问题的最佳做法。 为简单起见，此处仅提及有选择的选项：

* 请确保你是在其上安装 AAD Connect 的计算机上的管理员。 使用同一管理员凭据登录计算机。

* 在以下所有页保留默认选项，如果想要使用现有 SQL Server，则“使用现有 SQL Server”页除外。 以下是有关如何使用自定义安装选项的[更多详细信息](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)。 

    ![使用现有 SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 在以下页上，选择“新建 AD 帐户”选项，以避免与现有帐户的任何权限问题。

    ![AD 林帐户](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**常见问题**

* [本地 Active Directory 的连接问题](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)。

* [联机 Azure Active Directory 的连接问题](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)。

* [本地 Active Directory 的权限问题](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)。

## <a name="recommended-documents"></a>**建议的文档**
* [Azure AD Connect 的先决条件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [选择要使用的 Azure AD Connect 安装类型](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [通过快速设置开始使用 Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Azure AD Connect 的自定义安装](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect：从以前版本升级到最新版本](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect：什么是暂存服务器？](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [什么是 ADConnectivityTools PowerShell 模块？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什么是混合标识？](whatis-hybrid-identity.md)。





