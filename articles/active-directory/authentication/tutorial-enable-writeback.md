---
title: Azure AD 启用密码写回
description: 在本教程中，你将启用密码写回（作为 Azure AD Connect 的一部分）以将云发起的密码更改写回到本地 AD。
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 9512c800a35ad4a819c657b07227d781c63c6399
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163267"
---
# <a name="tutorial-enabling-password-writeback"></a>教程：启用密码写回

在本教程中，将为混合环境启用密码写回。 密码写回用来将 Azure Active Directory (Azure AD) 中的密码更改同步回本地 Active Directory 域服务 (AD DS) 环境。 密码写回是作为 Azure AD Connect 的一部分启用的，它提供了一种机制来将密码更改从 Azure AD 发送回现有的本地目录。 可以在[什么是密码写回](concept-sspr-writeback.md)一文中找到有关密码写回的内部工作的更多详细信息。

> [!div class="checklist"]
> * 在 Azure AD Connect 中启用密码写回选项
> * 在自助服务密码重置 (SSPR) 中启用密码写回选项

## <a name="prerequisites"></a>先决条件

* 能够访问至少分配有试用版许可证的有效 Azure AD 租户。
* 在 Azure AD 租户中具有全局管理员权限的帐户。
* 已配置的运行 [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) 的当前版本的一台现有服务器。
* 已完成了前面的自助服务密码重置 (SSPR) 教程。

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>在 Azure AD Connect 中启用密码写回选项

为启用密码写回，我们首先需要从安装有 Azure AD Connect 的服务器启用该功能。

1. 若要配置和启用密码写回服务，请登录 Azure AD Connect 服务器，并启动“Azure AD Connect”配置向导。
2. 在“欢迎”页上，选择“配置”。
3. 在“其他任务”页上，依次选择“自定义同步选项”和“下一步”。
4. 在“连接到 Azure AD”页上，输入全局管理员凭据，再选择“下一步”。
5. 在“连接目录”和“域/OU 筛选”页上，选择“下一步”。
6. 在“可选功能”页上，选中“密码写回”旁边的框，并选择“下一步”。
7. 在“已准备好进行配置”页上，选择“配置”，并等待进程完成。
8. 在配置完成后，选择“退出”。

## <a name="enable-password-writeback-option-in-sspr"></a>在 SSPR 中启用密码写回选项

在 Azure AD Connect 中启用密码写回功能只是故事的一半。 允许 SSPR 使用密码写回将完成整个循环，这将使对密码进行更改或重置的用户同样在本地设置该密码。

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 浏览到“Azure Active Directory”，单击“密码重置，然后选择“本地集成”。
3. 将“将密码写回到本地目录”选项设置为“是”。
4. 将“允许用户在不重置密码的情况下解锁帐户”选项设置为“是”。
5. 单击“保存”

## <a name="next-steps"></a>后续步骤

在本教程中，你已经为自助服务密码重置启用了密码写回。 请将 Azure 门户窗口保持打开并继续学习下一教程，在试点中推广解决方案之前配置与自助服务密码重置相关的更多设置。

> [!div class="nextstepaction"]
> [在 Windows 登录屏幕上启用 SSPR](tutorial-sspr-windows.md)
