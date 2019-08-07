---
title: 如何对 Azure 数据目录进行故障排除
description: 本文介绍 Azure 数据目录资源的常见疑难解答问题。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: f37110138ae62272e56b155b7e8a9aac2f80cdf8
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734585"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure 数据目录故障排除

本文介绍 Azure 数据目录资源的常见疑难解答问题。 

## <a name="functionality-limitations"></a>功能限制

使用 Azure 数据目录时, 以下功能受到限制:

- 不支持具有类型为**Guest 角色**的帐户。 不能将来宾帐户添加为 Azure 数据目录的用户, 并且来宾用户无法在 www.azuredatacatalog.com 使用该门户。

- 不支持使用 Azure 资源管理器模板或 Azure PowerShell 命令创建 Azure 数据目录资源。

- Azure 数据目录资源无法在 Azure 租户之间移动。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 策略配置

可能会遇到这种情况：可以登录 Azure 数据目录门户，但在尝试登录数据源注册工具时出现错误消息，导致无法登录。 当你在公司网络上或从公司网络外部进行连接时, 可能会发生此错误。

注册工具使用 *窗体身份验证* 针对 Azure Active Directory 验证用户登录。 若要成功登录，Azure Active Directory 管理员必须在 *全局身份验证策略*中启用窗体验证。

使用全局验证策略，可以单独为 Intranet 和 Extranet 连接启用身份验证，如下图所示。 如果未为要连接的网络启用表单身份验证, 则可能会发生登录错误。

 ![Azure Active Directory 全局身份验证策略](./media/troubleshoot-policy-configuration/global-auth-policy.png)

有关详细信息，请参阅 [Configuring authentication policies](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))（配置身份验证策略）。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 数据目录](data-catalog-get-started.md)
