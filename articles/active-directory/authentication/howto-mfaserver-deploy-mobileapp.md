---
title: Azure MFA 服务器移动应用 Web 服务 | Microsoft Docs
description: 将 MFA 服务器配置为使用 Microsoft Authenticator 应用向用户发送推送通知。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: b335ee50d86e125782b17210577eb22c664805fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330629"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure 多重身份验证服务器启用移动应用身份验证

Microsoft Authenticator 应用提供一个附加的带外验证选项。 Azure 多重身份验证会将通知推送到用户智能手机或平板电脑上的 Microsoft Authenticator 应用中，而不是在用户登录期间对用户进行自动电话呼叫或向其发送短信。 用户只需在该应用中点击“验证”（或输入 PIN 码并点击“身份验证”）即可完成登录。

在电话信号不佳导致接收存在问题时，建议使用移动应用进行双重验证。 将应用用作 OATH 令牌生成器时，它不需要任何网络或 Internet 连接。

> [!IMPORTANT]
> 如果已安装了 Azure 多重身份验证服务器 v8.x 或更高版本，则下面的大多数步骤都不是必需的。 可以按照[配置移动应用](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)中的步骤设置移动应用身份验证。

## <a name="requirements"></a>要求

若要使用 Microsoft Authenticator 应用，必须运行 Azure 多重身份验证服务器 v8.x 或更高版本

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure 多重身份验证服务器中配置移动应用设置

1. 在多重身份验证服务器控制台中，单击“用户门户”图标。 如果允许用户控制其身份验证方法，请在“设置”选项卡上的“允许用户选择方法”下，选中“移动应用”。 如果未启用此功能，则最终用户需要联系支持人员才能完成移动应用的激活。
2. 选中“允许用户激活移动应用”框。
3. 选中“允许用户注册”框。
4. 单击“移动应用”图标。
5. 在“帐户名称”字段中填充要在此帐户的移动应用程序中显示的公司或组织名称。
   ![MFA 服务器配置移动应用设置](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 多重身份验证与第三方 VPN 的高级方案](howto-mfaserver-nps-vpn.md)。
