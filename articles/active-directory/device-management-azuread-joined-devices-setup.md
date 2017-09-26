---
title: "设置已加入 Azure Active Directory 的设备 | Microsoft Docs"
description: "了解如何设置已加入 Azure Active Directory 的设备。"
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
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>设置已加入 Azure Active Directory 的设备

通过 Azure Active Directory (Azure AD) 中的设备管理，有助于确保用户使用满足安全性和符合性标准要求的设备访问资源。 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](device-management-introduction.md)。

使用[已加入 Azure AD 的设备](device-management-introduction.md#azure-ad-joined-devices)，可以让 Azure AD 控制组织拥有的 Windows 10 设备。 这些设备不得已[加入本地 AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)。

本主题介绍了如何向 Azure AD 注册 Windows 10 设备。 

## <a name="prerequisites"></a>先决条件

开始之前，应先验证：

- 是否有权将设备加入 Azure AD。

    ![已连接](./media/device-management-azuread-joined-devices-setup/21.png)

- 是否尚未超过每用户的设备数量上限 

    ![已连接](./media/device-management-azuread-joined-devices-setup/22.png)


有关详细信息，请参阅[配置设备设置](device-management-azure-portal.md#configure-device-settings)。



## <a name="what-you-should-know"></a>要点


- Windows 在 Azure AD 中的组织目录内注册设备。

- 可能需要通过多重身份验证质询。 IT 管理员可以设置此质询。

- Azure AD 会检查设备是否需要进行移动设备管理注册。 如果需要，它会注册设备。

- Windows 通过自动登录功能将托管用户重定向到桌面。

- 联合用户会重定向到 Windows 登录页，以便输入凭据。


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





