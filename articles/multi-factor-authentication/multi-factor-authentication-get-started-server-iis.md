---
title: "IIS 身份验证和 Azure MFA 服务器 | Microsoft Docs"
description: "本 Azure 多重身份验证页面会帮助你部署 IIS 身份验证和 Azure 多重身份验证服务器。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.openlocfilehash: ab6f9110dccd3cfc15092f535650e8d8cb1af13c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>配置适用于 IIS Web 应用的 Azure 多重身份验证服务器

使用 Azure 多重身份验证 (MFA) 服务器的 IIS 身份验证部分来启用并配置 IIS 身份验证，以便与 Microsoft IIS Web 应用程序集成。 Azure MFA 服务器将安装一个插件，该插件可以筛选向 IIS Web 服务器发出的用于添加 Azure 多重身份验证的请求。 IIS 插件支持基于窗体的身份验证和集成式 Windows HTTP 身份验证。 还可以配置受信任的 IP，使内部 IP 地址免于进行双因素身份验证。

![IIS 身份验证](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>将基于窗体的 IIS 身份验证用于 Azure 多重身份验证服务器
若要保护使用基于窗体的身份验证的 IIS Web 应用程序，请在 IIS Web 服务器上安装 Azure 多重身份验证服务器，并按以下过程配置该服务器：

1. 在 Azure 多重身份验证服务器中，单击左侧菜单中的 IIS 身份验证图标。
2. 单击“基于表单”选项卡。
3. 单击“添加”。
4. 要自动检测用户名、密码和域变量，请在“自动配置基于窗体的网站”对话框内输入登录 URL（例如 https://localhost/contoso/auth/login.aspx），并单击“确定”。
5. 如果所有用户均已导入或将导入到该服务器并接受多重身份验证，请选中“需要多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。
6. 如果无法自动检测到页变量，请单击“自动配置基于窗体的网站”对话框中的“手动指定”。
7. 在“添加基于窗体的网站”对话框中，将登录页的 URL 输入到“提交 URL”字段中并输入应用程序名称（可选）。 应用程序名称将出现在 Azure 多重身份验证报告中，并可能会显示在短信或移动应用身份验证消息中。
8. 选择正确的请求格式。 对于大多数 Web 应用程序来说，此项设置为“POST 或 GET”。
9. 输入用户名变量、密码变量和域变量（如果显示在登录页上）。 若要查找输入框的名称，请在 Web 浏览器中导航到登录页，右键单击该页并选择“查看源”。
10. 如果所有用户均已导入或将导入到该服务器并接受多重身份验证，请选中“需要 Azure 多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。
11. 单击“高级”以查看高级设置，包括：

  - 选择自定义拒绝页文件
  - 使用 Cookie 将成功的身份验证缓存到网站一段时间
  - 选择是否对 Windows 域、LDAP 目录 或 RADIUS 服务器就主凭据进行身份验证。

12. 单击“确定”返回到“添加基于窗体的网站”对话框。
13. 单击 **“确定”**。
14. 检测到或输入 URL 和页面变量后，网站数据会显示在基于表单的面板中。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>将集成 Windows 身份验证用于 Azure 多重身份验证服务器
要保护使用集成式 Windows HTTP 身份验证的 IIS Web 应用程序，请在 IIS Web 服务器上安装 Azure MFA 服务器，并按以下步骤配置该服务器：

1. 在 Azure 多重身份验证服务器中，单击左侧菜单中的 IIS 身份验证图标。
2. 单击“HTTP”选项卡。
3. 单击“添加”。
4. 在“添加基 URL”对话框中，输入执行 HTTP 身份验证的网站的 URL（例如 http://localhost/owa）并提供应用程序名称（可选）。 应用程序名称将出现在 Azure 多重身份验证报告中，并可能会显示在短信或移动应用身份验证消息中。
5. 调整空闲超时和会话时间上限（如果默认值不够）。
6. 如果所有用户均已导入或将导入到该服务器并接受多重身份验证，请选中“需要多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。
7. 如果需要，请选中“Cookie 缓存”框。
8. 单击 **“确定”**。

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>为 Azure 多重身份验证服务器启用 IIS 插件
配置基于窗体的身份验证或 HTTP 身份验证 URL 和设置后，请在 IIS 中选择应加载并启用 Azure 多重身份验证 IIS 插件的位置。 请按以下过程操作：

1. 如果在 IIS 6 上运行，单击“ISAPI”选项卡。选择在其中运行 Web 应用程序的网站（例如默认网站），以便为该网站启用 Azure 多重身份验证 ISAPI 筛选器插件。
2. 如果在 IIS 7 或更高版本上运行，单击“本机模块”选项卡。选择要在所需级别启用 IIS 插件的服务器、网站或应用程序。
3. 单击屏幕顶部的“启用 IIS 身份验证”框。 现在，Azure 多重身份验证将保护所选 IIS 应用程序。 确保已将用户导入该服务器。

## <a name="trusted-ips"></a>受信任的 IP
受信任的 IP 允许用户跳过对从特定 IP 地址或子网发起的网站请求的 Azure 多重身份验证。 例如，当用户从办公室登录时，你可能想让这些用户免除 Azure 多重身份验证。 为此，可将办公室子网指定为受信任的 IP 条目。 若要配置受信任的 IP，请使用以下过程：

1. 在“IIS 身份验证”部分中，单击“受信任的 IP”选项卡。
2. 单击“添加”。
3. 显示“添加受信任 IP”对话框时，选择“单个 IP”、“IP 范围”或“子网”单选按钮。
4. 输入应加入允许列表的 IP 地址、IP 地址范围或子网。 如果输入子网，请选择适当的网络掩码，并单击“确定”。 现已添加允许列表。
