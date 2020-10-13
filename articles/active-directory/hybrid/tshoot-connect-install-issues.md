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
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89275855"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>疑难解答：Azure AD Connect 安装问题

## <a name="recommended-steps"></a>**建议的步骤**
请查看适合你的 [Azure AD Connect 安装类型](./how-to-connect-install-select-installation.md)。 如果满足快速安装的条件，则强烈建议进行快速安装。 快速安装提供完成安装所需的最少选项，因此出现问题的可能性更小。 

但是，如果不满足快速安装的条件，并且必须执行自定义安装，则下文提供一些可用于避免常见问题的最佳做法。 为简单起见，此处仅提及有选择的选项：

* 请确保你是在其上安装 AAD Connect 的计算机上的管理员。 使用同一管理员凭据登录计算机。

* 在以下所有页保留默认选项，如果想要使用现有 SQL Server，则“使用现有 SQL Server”页除外。 以下是有关如何使用自定义安装选项的[更多详细信息](./how-to-connect-install-custom.md)。 

    ![使用现有 SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 在以下页上，选择“新建 AD 帐户”选项，以避免与现有帐户的任何权限问题。

    ![AD 林帐户](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**常见问题**

* [本地 Active Directory 的连接问题](./reference-connect-adconnectivitytools.md)。

* [联机 Azure Active Directory 的连接问题](./tshoot-connect-connectivity.md)。

* [本地 Active Directory 的权限问题](./how-to-connect-configure-ad-ds-connector-account.md)。

## <a name="recommended-documents"></a>**建议的文档**
* [Azure AD Connect 的先决条件](./how-to-connect-install-prerequisites.md)
* [选择要使用的 Azure AD Connect 安装类型](./how-to-connect-install-select-installation.md)
* [通过快速设置开始使用 Azure AD Connect](./how-to-connect-install-express.md)
* [Azure AD Connect 的自定义安装](./how-to-connect-install-custom.md)
* [Azure AD Connect：从旧版升级到最新版本](./how-to-upgrade-previous-version.md)
* [Azure AD Connect：什么是暂存服务器？](./plan-connect-topologies.md#staging-server)
* [什么是 ADConnectivityTools PowerShell 模块？](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什么是混合标识？](whatis-hybrid-identity.md)