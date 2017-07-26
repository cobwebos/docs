---
title: "Azure 数据目录先决条件 | Microsoft Docs"
description: "了解开始使用 Azure 数据目录的先决条件。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 6ae024e59d29d20c223243b71aceae1db7eefdf5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/21/2017


---
# <a name="azure-data-catalog-prerequisites"></a>Azure 数据目录先决条件

在设置 Azure 数据目录之前，需要注意一些事项。 别担心，此过程不会花费太长时间。

## <a name="azure-subscription"></a>Azure 订阅
只有 Azure 订阅的所有者或共同所有者才可以设置数据目录。

Azure 订阅可帮助组织云服务资源的访问权限，例如数据目录。 订阅还可帮助控制资源使用情况的报告、计费及付费方式。 每一个订阅可以有单独的计费和付款设置，因此，根据部门、项目、区域办事处等，可以有不同的订阅和计划。 每个云服务属于某一个订阅，故在设置 Azure 数据目录之前，必须先有一个订阅。 有关详细信息，请参阅 [Manage accounts, subscriptions, and administrative roles](../active-directory/active-directory-assign-admin-roles.md)（管理帐户、订阅和管理角色）。

## <a name="azure-active-directory"></a>Azure Active Directory
要设置 Azure 数据目录，须使用 Azure Active Directory (Azure AD) 用户账户登录。

Azure AD 为企业提供在云中和本地轻松管理标识与访问权限的方式。 用户可以使用单个公司帐户或学校帐户，单一登录到任何云和本地 Web 应用程序。 数据目录使用 Azure AD 对登录进行身份验证。 有关详细信息，请参阅[什么是 Azure Active Directory？](../active-directory/active-directory-whatis.md)。

> [!NOTE]
> 通过使用 [Azure 门户](http://portal.azure.com/)，用户可使用个人 Microsoft 帐户或 Azure Active Directory 工作或学校帐户进行登录。 若要使用 Azure 门户或[数据目录门户](http://www.azuredatacatalog.com)设置 Azure 数据目录，必须使用 Azure Active Directory 帐户而不是个人帐户进行登录。
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory 策略配置
可能会遇到这种情况：可以登录数据目录门户，但在尝试登录数据源注册工具时，出现错误消息，导致无法登录。 只有处于公司网络中时，或者从公司外部网络进行连接时，才可能出现这样的问题。

数据源注册工具使用窗体身份验证针对 Active Directory 验证用户凭据。 为帮助登录成功，Azure Active Directory 管理员必须在全局身份验证策略中启用窗体验证。

在全局验证策略中，可以单独为 Intranet 和 Extranet 连接启用身份验证，如下图所示。 如果未对网络连接位置启用窗体身份验证，登录时可能会出现错误。

 ![Active Directory 全局身份验证策略](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Configuring Authentication Policies](https://technet.microsoft.com/library/dn486781.aspx)（配置身份验证策略）。

