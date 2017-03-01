---
title: "声明感知应用 - Azure AD 应用代理 | Microsoft 文档"
description: "如何发布接受 ADFS 声明允许用户进行安全远程访问的本地 ASP.NET 应用程序。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: ff07a52f6a503f07f5919b63f345878571742cac


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>在应用程序代理中使用声明感知应用
声明感知应用执行目标为安全令牌服务 (STS) 的重定向操作，该服务会反过来请求用户提供凭据来交换令牌，然后才会将用户重定向到应用程序。 若要允许应用程序代理处理这些重定向，需执行以下步骤。

## <a name="prerequisites"></a>先决条件
在执行此过程之前，请确保充当声明感知应用重定向目标的 STS 在用户本地网络之外可用。

## <a name="azure-classic-portal-configuration"></a>Azure 经典门户配置
1. 根据[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)中的说明发布应用程序。
2. 在应用程序列表中，选择声明感知应用，然后单击“配置”。
3. 如果选择“传递”作为“预身份验证方法”，请确保选择“HTTPS”作为“外部 URL”方案。
4. 如果选择“Azure Active Directory”作为“预身份验证方法”，请选择“无”作为“内部身份验证方法”。

## <a name="adfs-configuration"></a>ADFS 配置
1. 打开“ADFS 管理”。
2. 转到“信赖方信任”，右键单击要使用应用程序代理进行发布的应用，然后选择“属性”。  

   ![信赖方信任右键单击应用名称 - 屏幕截图](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. 在“终结点”选项卡的“终结点类型”下，选择“WS-Federation”。
4. 在“受信任的 URL”下输入 URL（曾在应用程序代理的“外部 URL”下输入过），然后单击“确定”。  

   ![添加终结点 - 设置受信任的 URL 值 - 屏幕截图](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>后续步骤
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)
* [允许本地客户端应用与代理应用程序交互](active-directory-application-proxy-native-client.md)





<!--HONumber=Feb17_HO1-->


