---
title: "设置已注册 Azure Active Directory 的 Windows 10 设备 | Microsoft Docs"
description: "了解如何设置已注册 Azure Active Directory 的 Windows 10 设备。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>设置已注册 Azure Active Directory 的 Windows 10 设备

通过 Azure Active Directory (Azure AD) 中的设备管理，有助于确保用户使用满足安全性和符合性标准要求的设备访问资源。 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](device-management-introduction.md)。

使用[已注册 Azure AD 的设备](device-management-introduction.md#azure-ad-registered-devices)，可以启用自带设备办公 (BYOD) 方案，以便用户能够使用个人拥有的设备访问组织的资源。  

在 Azure AD 中，可以针对下列平台设置已注册 Azure AD 的设备：

- Windows 10
- iOS
- Android
- macOS。  

本主题介绍了如何向 Azure AD 注册 Windows 10 设备。 


## <a name="prerequisites"></a>先决条件

开始之前，应先验证：

- 是否有权注册设备 

    ![注册](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- 是否尚未超过每用户的设备数量上限 

    ![注册](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

有关详细信息，请参阅[配置设备设置](device-management-azure-portal.md#configure-device-settings)。

## <a name="what-you-should-know"></a>要点

注册设备时，请注意以下事项：

- Windows 在 Azure AD 中的组织目录内注册设备。

- 可能需要通过多重身份验证质询。 IT 管理员可以设置此质询。

- Azure AD 会检查设备是否需要进行移动设备管理注册。 如果需要，它会注册设备。

- Windows 通过自动登录功能将托管用户重定向到桌面。

- 联合用户会重定向到 Windows 登录页，以便输入凭据。


## <a name="register-a-device"></a>注册设备

若要向 Azure AD 注册 Windows 10 设备，请完成以下步骤。 如果已成功向 Azure AD 注册设备，“访问工作或学校数据”页上会显示“工作或学校帐户”条目，从而指明这一点。

![注册](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


注册 Windows 10 设备的具体步骤：

1. 在“开始”菜单中，单击“设置”。

    ![选择“设置”](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. 单击“帐户”。

    ![选择“帐户”](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. 单击“访问工作单位或学校”。

    ![选择“访问工作或学校数据”](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. 在“访问工作或学校数据”页中，单击“连接”。

    ![“访问工作或学校数据”页](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. 在“设置工作或学校帐户”页中，输入帐户名称（例如，someone@example.com），再单击“下一步”。

    ![“设置工作或学校帐户”页](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. 在“输入密码”页中，输入密码，再单击“下一步”。

    ![输入密码](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. 在“已设置完毕”页中，单击“完成”。

    ![“已设置完毕”页](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>验证

若要验证设备是否已加入 Azure AD，请查看设备上的“访问工作或学校数据”页。

![工作或学校帐户状态](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

也可以检查 Azure AD 门户上的设备设置。

![Azure AD 注册设备](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Azure Active Directory 中的设备管理简介](device-management-introduction.md)

- [使用 Azure 门户管理设备](device-management-azure-portal.md)





