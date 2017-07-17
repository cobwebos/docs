---
title: "Azure AD Privileged Identity Management 入门 | Microsoft Docs"
description: "了解如何在 Azure 门户中使用 Azure Active Directory Privileged Identity Management 应用程序管理特权标识。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017

---
# 开始使用 Azure AD Privileged Identity Management
<a id="start-using-azure-ad-privileged-identity-management" class="xliff"></a>
使用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视组织内的访问。 此范围包括访问 Azure AD 和其他 Microsoft Online Services（如 Office 365 或 Microsoft Intune）中的资源。

本文介绍了如何将 Azure AD PIM 应用添加到 Azure 门户仪表板。

## 添加 Privileged Identity Management 应用程序
<a id="add-the-privileged-identity-management-application" class="xliff"></a>
在使用 Azure AD Privileged Identity Management 之前，需要将应用程序添加到 Azure 门户仪表板。

1. 以目录的全局管理员身份登录 [Azure 门户](https://portal.azure.com/) 。
2. 如果你的组织有多个目录，请在 Azure 门户的右上角选择你的用户名。 选择要在其中使用 PIM 的目录。
3. 选择“更多服务”，然后使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，然后单击“创建”。 Privileged Identity Management 应用程序打开。

如果你是第一个在目录中使用 Azure AD Privileged Identity Management 的人，系统会自动在目录中为你分配“安全管理员”和“特权角色管理员”角色。 只有特权角色管理员才能管理用户的角色分配。 此外，还可以选择运行[安全向导](active-directory-privileged-identity-management-security-wizard.md)。 系统会引导你完成初始的发现和分配体验。

## 导航到你的任务
<a id="navigate-to-your-tasks" class="xliff"></a>
设置 Azure AD Privileged Identity Management 后，每次打开应用程序时均会看到导航边栏选项卡。 使用此边栏选项卡可完成标识管理任务。

![PIM 的顶级任务 - 屏幕快照](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* “我的角色”可将你带到分配给你的角色列表。 你可在此部分中激活任何符合条件的角色。
* “审批请求(预览)”会显示由目录中的用户提交的一系列挂起的激活请求。 [了解详细信息。](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* “挂起的请求(预览)”会显示当前提交的需激活的任何请求。
* “审阅访问权限”会将你带到需要完成的任何挂起的访问审阅，无论你是审阅自己还是审阅其他人的访问权限。
* “Azure AD 目录角色”（位于“管理”部分下面）是特权角色管理员用于管理角色分配、更改角色激活设置、开始访问审阅等的仪表板。 如果不是特权角色管理员，则会禁用此仪表板中的选项。

## 后续步骤
<a id="next-steps" class="xliff"></a>
[Azure AD Privileged Identity Management 概述](active-directory-privileged-identity-management-configure.md) 包括有关如何管理组织中管理访问权限的更多详细信息。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

