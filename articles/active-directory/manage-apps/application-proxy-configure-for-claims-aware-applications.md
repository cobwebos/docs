---
title: 声明感知应用 - Azure AD 应用代理 | Microsoft 文档
description: 如何发布接受 ADFS 声明允许用户进行安全远程访问的本地 ASP.NET 应用程序。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 1618200ce3d96013f3d7b05db53163c993efc69a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161570"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>在应用程序代理中使用声明感知应用
[声明感知应用](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx)对安全令牌服务 (STS) 执行重定向。 STS 请求用来自用户的凭据交换令牌，然后将用户重定向到应用程序。 使应用程序代理使用这些重定向有几种方式。 使用本文配置声明感知应用的部署。 

## <a name="prerequisites"></a>先决条件
确保充当声明感知应用重定向目标的 STS 在本地网络之外可用。 将 STS 通过代理公开或允许外部连接，可以使其可用。 

## <a name="publish-your-application"></a>发布应用程序

1. 根据[使用应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)中的说明发布应用程序。
2. 导航到门户中的应用程序页，选择“单一登录”。
3. 如果选择“Azure Active Directory”作为“预身份验证方法”，请选择“已禁用 Azure AD 单一登录”作为“身份验证方法”。 如果选择“传递”作为“预身份验证方法”，则无需更改任何内容。

## <a name="configure-adfs"></a>配置 ADFS

可以使用两种方式之一配置声明感知应用的 ADFS。 第一种方式是使用自定义域。 第二种方式是使用 WS 联合身份验证。 

### <a name="option-1-custom-domains"></a>方法 1：自定义域

如果应用程序的所有内部 URL 都是完全限定的域名 (FQDN)，则可以配置应用程序的[自定义域](application-proxy-configure-custom-domain.md)。 使用自定义域创建与内部 URL 相同的外部 URL。 外部 URL 匹配内部 URL 时，无论用户是在本地还是远程，STS 重定向都会起作用。 

### <a name="option-2-ws-federation"></a>方法 2：WS 联合身份验证

1. 打开“ADFS 管理”。
2. 转到“信赖方信任”，右键单击要使用应用程序代理进行发布的应用，并选择“属性”。  

   ![信赖方信任右键单击应用名称 - 屏幕截图](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. 在“终结点”选项卡的“终结点类型”下，选择“WS-Federation”。
4. 在“受信任的 URL”下输入 URL（曾在应用程序代理的“外部 URL”下输入过），并单击“确定”。  

   ![添加终结点 - 设置受信任的 URL 值 - 屏幕截图](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>后续步骤
* 为非声明感知应用程序[启用单一登录](application-proxy-single-sign-on.md)
* [允许本地客户端应用与代理应用程序交互](application-proxy-configure-native-client-application.md)


