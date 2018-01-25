---
title: "首次运行期间使用 Azure AD 加入新的 Windows 10 设备 | Microsoft Docs"
description: "本主题说明用户如何在首次运行体验期间设置 Azure AD Join。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0e71df2333dee9c4eb9935d3397d343be246be65
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>首次运行期间使用 Azure AD 加入新的 Windows 10 设备

使用 Azure Active Directory (Azure AD) 中的设备管理，可以确保用户从满足安全性和符合性标准的设备访问资源。 有关更多详细信息，请参阅 [Azure Active Directory 中的设备管理简介](device-management-introduction.md)。

对于 Windows 10，可在首次运行体验 (FRX) 期间将新的设备加入 Azure AD。  
通过这样可将打包的设备分发给员工或学生。

如果设备安装了 Windows 10 专业版或 Windows 10 企业版，则体验将默认为公司所拥有设备的设置过程。

Windows 全新体验中不支持加入本地 Active Directory (AD) 域。 如果打算将计算机加入 AD 域，在设置期间就应选择链接“使用本地帐户设置 Windows”。 然后可通过计算机上的设置加入域。
 


## <a name="before-you-begin"></a>开始之前

若要加入 Windows 10 设备，必须配置设备注册服务以允许注册设备。 除具有在 Azure AD 租户中加入设备的权限外，注册设备的数量必须少于所配置的最大数。 有关详细信息，请参阅[配置设备设置](device-management-azure-portal.md#configure-device-settings)。

## <a name="joining-a-device"></a>加入设备

**FRX 期间将 Windows 10 设备加入 Azure AD：**


1. 打开新设备并启动设置过程时，应该会看到“正在准备”消息。 请按照提示来设置设备。

2. 首先，自定义区域和语言。 然后接受 Microsoft 软件许可条款。
 
    ![为区域自定义](./media/device-management-azuread-joined-devices-frx/01.png)

3. 选择要用来连接到 Internet 的网络。

4. 单击“此设备属于我的组织”。 

    ![“谁是这台电脑的所有者”屏幕](./media/device-management-azuread-joined-devices-frx/02.png)

5. 输入组织提供的凭据，然后单击“登录”。

    ![登录屏幕](./media/device-management-azuread-joined-devices-frx/03.png)

6. 设备会查找 Azure AD 中的匹配租户。 如果在联盟域中，系统会将你重定向到本地安全令牌服务 (STS) 服务器，例如，Active Directory 联合身份验证服务 (AD FS)。

7. 如果是非联盟域中的用户，请直接在 Azure AD 托管页上输入凭据。 

8. 系统会提示完成多重身份验证质询。 
 
9. Azure AD 会检查是否需要在移动设备管理中进行注册。

10. Windows 会在 Azure AD 的组织目录中注册设备，并将其注册到移动设备管理中（如果适用）。

11. 相应流程：
    - 如果为托管用户，Windows 会通过自动登录过程你将转到桌面。

    - 如果为联合用户，则会定向到 Windows 登录屏幕以输入凭据。

## <a name="verification"></a>验证

若要验证设备是否已加入 Azure AD，请查看 Windows 设备上的“访问工作单位或学校”。 此对话框应会指示你已连接到 Azure AD 目录。

![访问工作单位或学校](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>后续步骤

- 有关更多详细信息，请参阅 [Azure Active Directory 中的设备管理简介](device-management-introduction.md)。

- 有关在 Azure AD 门户中管理设备的详细信息，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)。
