---
title: 如何配置 Azure Active Directory 策略的 Azure 数据目录
description: 可能会遇到的情况，其中你可以登录到 Azure 数据目录门户中，但当您尝试登录到数据源注册工具时，会遇到一条错误消息。
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: 558f8845f5469bf157188e20f1ec65a07ff8355f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362971"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 策略配置

可能会遇到这种情况：可以登录 Azure 数据目录门户，但在尝试登录数据源注册工具时出现错误消息，导致无法登录。 当您在公司网络上或者要从连接公司网络外部时，可能会发生此错误。

## <a name="registration-tool"></a>注册工具

注册工具使用 *窗体身份验证* 针对 Azure Active Directory 验证用户登录。 若要成功登录，Azure Active Directory 管理员必须在 *全局身份验证策略*中启用窗体验证。

使用全局验证策略，可以单独为 Intranet 和 Extranet 连接启用身份验证，如下图所示。 如果要连接的网络未启用窗体身份验证，则可能会出现登录错误。

 ![Azure Active Directory 全局身份验证策略](./media/troubleshoot-policy-configuration/global-auth-policy.png)

有关详细信息，请参阅 [Configuring authentication policies](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))（配置身份验证策略）。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 数据目录](data-catalog-get-started.md)