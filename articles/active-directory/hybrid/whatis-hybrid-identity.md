---
title: 什么是使用 Azure Active Directory 的混合标识？
description: 混合标识是指在本地和云中具有用于身份验证和授权的通用用户标识。
keywords: Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c43238d44b2309d105ef14e696a5a16848d0b58
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896831"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>什么是使用 Azure Active Directory 的混合标识？

如今，企业和公司越来越成为本地应用程序和云应用程序的混合体。  用户需要同时访问位于本地和云中的这些应用程序。 同时在本地和云中管理用户面临一些很有挑战性的方案。 

Microsoft 的标识解决方案涵盖了本地功能和基于云的功能。  这些解决方案创建一个通用用户标识，用于针对所有资源进行身份验证和授权，无论资源位于什么位置。 我们称此为**混合标识**。

借助 Azure AD 混合标识和混合标识管理，这些方案将成为可能。

若要通过 Azure AD 实现混合标识，可以根据你的具体方案使用三种身份验证方法之一。   这三种方法是： 

- **[密码哈希同步 (PHS)](whatis-phs.md)**  
- **[直通身份验证 (PTA)](how-to-connect-pta.md)**  
- **[联合 (AD FS)](whatis-fed.md)** 

这些身份验证方法还提供[单一登录](how-to-connect-sso.md)功能。  单一登录可使连接到企业网络的企业设备上的用户自动登录。

有关详细信息，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。 

## <a name="common-scenarios-and-recommendations"></a>常见方案和建议 

下面是一些常见的混合标识和访问管理方案，其中每个方案都包含有关适合的混合标识选项的建议。 

|我需要：|PHS 和 SSO<sup>1</sup>| PTA 和 SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|将本地 Active Directory 中创建的新用户、联系人和组帐户自动同步到云。|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|为 Office 365 混合方案设置我的租户。|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|使用户能够使用其本地密码登录并访问云服务。|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|使用公司凭据实现单一登录。|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)|  
|确保未在云中存储密码哈希。| |![建议](./media/whatis-hybrid-identity/ic195031.png)|![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|启用基于云的多重身份验证解决方案。|![建议](./media/whatis-hybrid-identity/ic195031.png)|![建议](./media/whatis-hybrid-identity/ic195031.png)|![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|启用本地多重身份验证解决方案。| | |![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|支持用户使用智能卡身份验证。<sup>4</sup>| | |![建议](./media/whatis-hybrid-identity/ic195031.png)| 
|在 Office 门户和 Windows 10 桌面上显示密码到期通知。| | |![建议](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> 使用单一登录的密码哈希同步。 
> 
> <sup>2</sup> 传递身份验证和单一登录。  
> 
> <sup>3</sup> 使用 AD FS 的联合单一登录。  
>  
> <sup>4</sup> AD FS 可与企业 PKI 集成，允许使用证书登录。 这些证书可以是通过受信任预配通道（如 MDM、GPO 或智能卡证书（包括 PIV/CAC 卡）或 Hello 企业版（信任证书））部署的软证书。 有关智能卡身份验证支持的详细信息，请参阅[此博客](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)。 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>使用 Azure AD Connect 的许可证要求

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md) 
- [什么是密码哈希同步 (PHS)？](whatis-phs.md) 
- [什么是直通身份验证 (PTA)？](how-to-connect-pta.md) 
- [什么是联合身份验证？](whatis-fed.md) 
- [什么是单一登录？](how-to-connect-sso.md) 

