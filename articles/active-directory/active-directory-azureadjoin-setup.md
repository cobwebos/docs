---
title: "为用户设置 Azure AD Join | Microsoft Docs"
description: "说明管理员如何为本地目录和设备注册设置 Azure AD Join。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0480c4f89b6036897bc8d7eff81b56262ea8806c
ms.openlocfilehash: e4e0fa77552c4df2ea5bb9ddae916e7c661824d1
ms.contentlocale: zh-cn
ms.lasthandoff: 02/23/2017

---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>在组织中设置 Azure AD Join
在设置 Azure Active Directory Join (Azure AD Join) 之前，需要将用户的本地目录同步到云，或者在 Azure AD 中手动创建托管帐户。

有关将本地用户同步到 Azure AD 的详细说明，请参阅 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。

若要在 Azure AD 中手动创建和管理用户，请参阅 [Azure AD 中的用户管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)。

## <a name="set-up-device-registration"></a>设置设备注册
1. 以管理员身份登录到 Azure 门户。
2. 在左窗格中选择“Active Directory”。
3. 在“目录”选项卡中选择你的目录。
4. 选择“配置”  选项卡。
5. 转到“设备”  部分。
6. 在“设备”选项卡上，设置以下项：   
   * **每个用户的最大设备数**：选择用户可在 Azure AD 中拥有的最大设备数。  如果用户达到此配额，则在删除一个或多个现有设备之前，他们无法添加其他设备。
   * **需要进行 MULTI-FACTOR AUTH 才能加入设备**：设置是否需要用户提供第二个身份验证因素才能将其设备加入 Azure AD。 有关 Azure 多重身份验证的详细信息，请参阅[云中的 Azure 多重身份验证入门](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)。
   * **用户可将设备加入 AZURE AD**：选择可将设备加入 Azure AD 的用户和组。
   * **已加入 AZURE AD 的设备上的其他管理员**：使用 Azure AD Premium 或企业移动性套件 (EMS)，可以选择要将设备的本地管理员权限授予哪些用户。 默认情况下，会将本地管理员权限授予全局管理员和设备所有者。

<center>![设置设备注册](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

为用户设置 Azure AD Join 后，他们可以通过其企业设备或个人设备连接到 Azure AD。

下面是可用于使用户能够设置 Azure AD Join 的三种方案：

* 用户将公司拥有的设备直接加入 Azure AD。
* 用户将公司拥有的设备加入本地 Active Directory 域，然后将该设备扩展到 Azure AD。
* 用户在个人设备上将工作或学校帐户添加到 Windows

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)


