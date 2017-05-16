---
title: "Azure 数据目录先决条件 | Microsoft Docs"
description: "Azure 数据目录前提条件 - 入门 Azure 数据目录所需的内容。"
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
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 412063692e87fe71890f62bcc18b99b317a80986
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="azure-data-catalog-prerequisites"></a>Azure 数据目录先决条件
## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>开始使用 Azure 数据目录需要什么？
可以设置 **Azure 数据目录**之前，需要注意一些事项。 不必担心 – 这不会花很多时间！

## <a name="azure-subscription"></a>Azure 订阅
只有 Azure 订阅的所有者或共同所有者才可以设置 Azure 数据目录。

Azure 订阅可帮助组织云服务资源的访问权限，例如 Azure 数据目录。 它们还可帮助控制如何根据资源使用量生成报告、计费及付费。 每一个订阅可以有不同的计费和付款设置，因此，根据部门、项目、区域办事处等，可以有不同的订阅和不同的计划。 每个云服务属于某个订阅，在设置 Azure 数据目录之前，必须先有订阅。 有关详细信息，请参阅[管理帐户、订阅和管理角色](../active-directory/active-directory-assign-admin-roles.md)。

## <a name="azure-active-directory"></a>Azure Active Directory
若要设置 Azure 数据目录，必须使用 Azure Active Directory 用户帐户登录。

Azure Active Directory (Azure AD) 为企业提供在云中和本地轻松管理标识与访问权限的方式。 用户可以使用单个公司帐户或学校帐户，单一登录到任何云和本地 Web 应用程序。 Azure 数据目录使用 Azure AD 验证登录。 有关详细信息，请参阅 [What is Azure Active Directory](../active-directory/active-directory-whatis.md)（什么是 Azure Active Directory）。

> [!NOTE]
> [Azure 门户](http://portal.azure.com/)允许用户使用个人 Microsoft 帐户或 Azure Active Directory 工作或学校帐户进行登录。 若要使用 Azure 门户或[数据目录门户](http://www.azuredatacatalog.com)设置 Azure 数据目录，必须使用 Azure Active Directory 帐户而不是个人帐户进行登录。
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory 策略配置
在某些情况下，用户可能会遇到这种情况：可以登录 Azure 数据目录门户，但在尝试登录数据源注册工具时出现错误消息，导致无法登录。 只有用户处于公司网络时，或者从公司外部网络进行连接时才可能出现这样的错误行为。

数据源注册工具使用窗体身份验证针对 Active Directory 验证用户登录。 若要成功登录，Active Directory 管理员必须在全局身份验证策略中启用窗体身份验证。

使用全局验证策略，可以单独为 Intranet 和 Extranet 连接启用身份验证方法，如下图所示。 如果用户连接的网络未启用窗体身份验证，可能会发生登录错误。

 ![Active Directory 全局身份验证策略](./media/data-catalog-prerequisites/global-auth-policy.png)

有关详细信息，请参阅 [Configuring Authentication Policies](https://technet.microsoft.com/library/dn486781.aspx)（配置身份验证策略）。

