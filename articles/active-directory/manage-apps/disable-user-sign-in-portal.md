---
title: 在 Azure Active Directory 中对企业应用禁用用户登录 | Microsoft Docs
description: 如何禁用企业应用程序，防止用户在 Azure Active Directory 中登录该程序
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90000f34ff247fdd5939dc19971c170aa4b70386
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824665"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中对企业应用禁用用户登录
很容易地禁用企业应用程序，以便任何用户可以不登录到它在 Azure Active Directory (Azure AD) 中。 需要适当的权限来管理企业应用。 并且，你必须是目录全局管理员。

## <a name="how-do-i-disable-user-sign-ins"></a>如何禁用用户登录？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
1. 上**Azure Active Directory** -  ***directoryname***窗格 （即，正在管理的目录的 Azure AD 窗格），选择**企业应用程序**.
1. 上**企业应用程序-所有应用程序**窗格中，您将看到一系列你可以管理的应用。 选择一个应用。
1. 在 ***appname*** 窗格（即标题中包含所选应用的名称的窗格）中，选择“属性”。
1. 在“appname - 属性”窗格中，对“启用以让用户登录?”设置选择“否”。
1. 选择“保存”命令。

## <a name="next-steps"></a>后续步骤
* [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [更改企业应用的名称或徽标](change-name-or-logo-portal.md)
