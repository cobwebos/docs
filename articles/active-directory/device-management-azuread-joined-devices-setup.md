---
title: "如何配置已加入 Azure Active Directory 的设备 | Microsoft Docs"
description: "了解如何配置已加入 Azure Active Directory 的设备。"
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
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 303660fa53919ec22a032931aab729fd36676b82
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-configure-azure-active-directory-joined-devices"></a>如何配置已加入 Azure Active Directory 的设备

使用 Azure Active Directory (Azure AD) 中的设备管理，可以确保用户从满足安全性和符合性标准的设备访问资源。 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](device-management-introduction.md)。

若要使工作单位的 Windows 10 设备受 Azure AD 控制，可通过配置已加入 Azure AD 的设备实现这一点。 本主题提供了相关步骤。 


## <a name="prerequisites"></a>先决条件

若要加入 Windows 10 设备，必须配置设备注册服务以允许注册设备。 除具有在 Azure AD 租户中加入设备的权限外，注册设备的数量必须少于所配置的最大数。 有关详细信息，请参阅[配置设备设置](device-management-azure-portal.md#configure-device-settings)。



## <a name="what-you-should-know"></a>要点


- Windows 将组织目录中的设备加入到 Azure AD。

- 可能需要完成多重身份验证质询。 该质询可由 IT 管理员配置。

- Azure AD 会检查设备是否需要移动设备管理注册，并且如果适用，则会注册设备。

- 如果是托管用户，Windows 会通过自动登录你将转到桌面。

- 如果是联合用户，需要使用凭据登录。


## <a name="joining-a-device"></a>加入设备

本部分提供了将 Windows 10 设备加入到 Azure AD 的步骤。 如果已成功将设备加入到 Azure AD，“访问工作单位或学校”对话框会通过“已连接到 \<Azure AD\>”条目进行指示。

![已连接](./media/device-management-azuread-joined-devices-setup/13.png)


**加入 Windows 10 设备：**

1. 在“开始”菜单中，单击“设置”。

    ![设置](./media/device-management-azuread-joined-devices-setup/01.png)

2. 单击“帐户”。

    ![帐户](./media/device-management-azuread-joined-devices-setup/02.png)


3. 单击“访问工作单位或学校”。

    ![访问工作单位或学校](./media/device-management-azuread-joined-devices-setup/03.png)

4. 在“访问工作单位或学校”对话框中，单击“连接”。

    ![连接](./media/device-management-azuread-joined-devices-setup/04.png)


5. 在“设置工作或学校帐户”对话框中，单击“将此设备加入 Azure Active Directory”。

    ![连接](./media/device-management-azuread-joined-devices-setup/08.png)


6. 在“开始登录”对话框中，输入帐户名称（例如 someone@example.com），然后单击“下一步”。

    ![开始登录](./media/device-management-azuread-joined-devices-setup/10.png)


6. 在“输入密码”对话框中输入密码，然后单击“登录”。

    ![输入密码](./media/device-management-azuread-joined-devices-setup/05.png)


7. 在“确保这是你的组织”对话框中，单击“加入”。

    ![确保这是你的组织](./media/device-management-azuread-joined-devices-setup/11.png)


8. 在“已经完成全部设置”对话框中，单击“完成”。

    ![已经完成全部设置](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>验证

若要验证设备是否已加入 Azure AD，可查看设备上的“访问工作单位或学校”。

![已连接](./media/device-management-azuread-joined-devices-setup/13.png)

或者，可运行如下命令：`dsregcmd /status`  
如果设备已成功加入，则 AzureAdJoined 为“是”。

![已连接](./media/device-management-azuread-joined-devices-setup/14.png)

此外，还可在 Azure AD 门户上查看设备设置。

![已连接](./media/device-management-azuread-joined-devices-setup/15.png)

有关详细信息，请参阅[查找设备](device-management-azure-portal.md#locate-devices)。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅： 

- [Azure Active Directory 中的设备管理简介](device-management-introduction.md)
- [使用 Azure 门户管理设备](device-management-azure-portal.md)
- 




