---
title: "安装期间使用 Azure AD 设置新设备 | Microsoft 文档"
description: "本主题说明用户如何在首次运行体验期间设置 Azure AD Join。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a6227096559af9b9beba7081ba770db80189dc18


---
# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>安装期间使用 Azure AD 设置新设备
在 Windows 10 中，用户可以在首次运行体验 (FRX) 期间将其设备加入 Azure Active Directory (Azure AD)。 这样，组织就可以将打包的设备分发给员工或学生，或者让他们选择自己的设备 (CYOD)。
如果设备安装了 Windows 10 专业版或 Windows 10 企业版，则体验将默认为公司所拥有设备的设置过程。

## <a name="to-join-a-device-to-azure-ad"></a>将设备加入 Azure AD
1. 打开新设备并启动设置过程时，应该会看到“正在准备”消息。 请按照提示来设置你的设备。
2. 首先，自定义你的区域和语言。 然后接受 Microsoft 软件许可条款。
   ![为你的区域自定义](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. 选择要用来连接到 Internet 的网络。
4. 选择你是使用个人设备还是公司拥有的设备。 如果是公司拥有的设备，则单击“此设备属于我的组织”。 随后将启动 Azure AD Join 体验。 如果你使用的是 Windows 10 专业版，就会看到以下屏幕。
   <center>
   ![谁拥有此电脑屏幕](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)
5. 输入组织提供给你的凭据。
   <center>
   ![登录屏幕](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6. 输入用户名后，Azure AD 中会出现匹配的租户。 如果你在联盟域中，系统会将你重定向到本地安全令牌服务 (STS) 服务器（例如，Active Directory 联合身份验证服务 (AD FS)）。
7. 如果你是非联盟域中的用户，请直接在 Azure AD 托管页上输入你的凭据。 如果已配置公司品牌，你还会看到组织徽标和支持文本。
8. 系统会提示你完成多重身份验证质询。 该质询可由 IT 管理员配置。
9. Azure AD 会检查该用户/设备是否需要在移动设备管理中注册。
10. Windows 会在 Azure AD 的组织目录中注册设备，并将其注册到移动设备管理中（如果适用）。
11. 如果你是托管用户，Windows 会通过自动登录过程将你转到桌面。
12. 如果你是联合用户，则会定向到 Windows 登录屏幕以输入凭据。

> [!NOTE]
> 不支持在 Windows 全新体验期间加入本地 Windows Server Active Directory 域。 因此，如果打算将计算机加入域，则应改为选择链接“使用本地帐户设置 Windows”。 然后，你就可以像往常一样通过计算机上的设置加入域。
> 
> 

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [通过 Microsoft Passport 在不使用密码的情况下进行身份验证](active-directory-azureadjoin-passport.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO5-->


