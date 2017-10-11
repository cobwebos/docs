---
title: "故障排除：“Active Directory”项缺失或不可用 | Microsoft Docs"
description: "Azure 管理门户中未显示 Active Directory 菜单项时怎么办。"
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>故障排除：“Active Directory”项缺失或不可用
关于使用 Azure Active Directory 功能和服务的很多说明都以“转到 Azure 管理门户并单击‘Active Directory’”开头。 但是，如果未出现 Active Directory 扩展或菜单项或者它被标记为**不可用**，该怎么办？ 本主题旨在提供帮助。 其中描述了 **Active Directory** 未出现或不可用的情况，并解释了如何继续执行操作。

## <a name="active-directory-is-missing"></a>缺少 Active Directory
通常，**Active Directory** 项出现在左侧导航菜单中。 Azure Active Directory 过程中的说明假定可以看见该项。

![屏幕截图：Azure 中的 Active Directory](./media/active-directory-troubleshooting/typical-view.png)

如果以下任一情况属实，Active  Directory 项出现在左侧导航菜单中。 否则，该项不会出现。

* 当前用户已使用 Microsoft 帐户（以前称为 Windows Live ID）登录。
  
    或
* Azure 租户具有目录，且当前帐户为目录管理员。
  
    或
* Azure 租户至少有一个 Azure AD 访问控制 (ACS) 命名空间。 有关详细信息，请参阅[“访问控制”命名空间](https://msdn.microsoft.com/library/azure/gg185908.aspx)。
  
    或
* Azure 租户至少有一个 Azure 多重身份验证提供程序。 有关详细信息，请参阅[管理 Azure 多重身份验证提供程序](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)。

若要创建访问控制命名空间或多重身份验证提供程序，请单击“+新建” > “应用服务” > “Active Directory”。

要获取对目录的管理权限，请让管理员为帐户分配管理员角色。 有关详细信息，请参阅[分配管理员角色](active-directory-assign-admin-roles.md)。

## <a name="active-directory-is-not-available"></a>Active Directory 不可用
当单击“+新建” > “应用服务”时，将显示“Active Directory”项。 具体而言，当前用户可以使用任何 Active Directory 功能（如“目录”、“访问控制”或“Multi-Factor Auth 提供程序”）时，会显示 Active Directory 项。

但是，在加载页面时，该项将灰显或标记为“不可用”。 这是一种暂时性的状态。 只需等待几秒，该项便可供使用。 如果延迟时间过长，刷新网页通常就会解决问题。

![屏幕截图：Active Directory 不可用](./media/active-directory-troubleshooting/not-available.png)

