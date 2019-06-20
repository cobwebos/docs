---
title: 如何排查 Azure 数据目录
description: 本文介绍 Azure 数据目录资源的常见故障排除问题。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203510"
---
# <a name="troubleshooting-azure-data-catalog"></a>故障排除的 Azure 数据目录

本文介绍 Azure 数据目录资源的常见故障排除问题。 

## <a name="functionality-limitations"></a>功能限制

在使用 Azure 数据目录时，以下功能会受到限制：

- 类型为科目**来宾角色**不受支持。 不能将来宾帐户添加为 Azure 数据目录的用户和来宾用户不能使用门户在 www.azuredatacatalog.com。

- 不支持创建 Azure 数据目录资源使用 Azure 资源管理器模板或 Azure PowerShell 命令。

- 不能 Azure 租户之间移动 Azure 数据目录资源。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 策略配置

可能会遇到这种情况：可以登录 Azure 数据目录门户，但在尝试登录数据源注册工具时出现错误消息，导致无法登录。 当您在公司网络上或者要从连接公司网络外部时，可能会发生此错误。

注册工具使用 *窗体身份验证* 针对 Azure Active Directory 验证用户登录。 若要成功登录，Azure Active Directory 管理员必须在 *全局身份验证策略*中启用窗体验证。

使用全局验证策略，可以单独为 Intranet 和 Extranet 连接启用身份验证，如下图所示。 如果要连接的网络未启用窗体身份验证，则可能会出现登录错误。

 ![Azure Active Directory 全局身份验证策略](./media/troubleshoot-policy-configuration/global-auth-policy.png)

有关详细信息，请参阅 [Configuring authentication policies](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))（配置身份验证策略）。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 数据目录](data-catalog-get-started.md)
