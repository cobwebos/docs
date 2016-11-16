---
title: "IIS 身份验证和 Azure Multi-Factor Authentication 服务器"
description: "本 Azure Multi-Factor Authentication 页面将会帮助你部署 IIS 身份验证和 Azure Multi-Factor Authentication 服务器。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 138e23a0964c0e540c6b2ddf8891aa9af6782233


---
# <a name="iis-authentication"></a>IIS 身份验证
你可以使用 Azure Multi-Factor Authentication 服务器的“IIS 身份验证”部分来启用并配置 IIS 身份验证，以便与 Microsoft IIS Web 应用程序集成。 Azure Multi-Factor Authentication 服务器将安装一个插件，该插件可以筛选向 IIS Web 服务器发出的用于添加 Azure Multi-Factor Authentication 的请求。 IIS 插件支持基于窗体的身份验证和集成式 Windows HTTP 身份验证。 还可以配置受信任的 IP，使内部 IP 地址免于进行双因素身份验证。

![IIS 身份验证](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-formbased-iis-authentication-with-azure-multifactor-authentication-server"></a>将基于窗体的 IIS 身份验证用于 Azure Multi-Factor Authentication 服务器
若要保护使用基于窗体的身份验证的 IIS Web 应用程序，请在 IIS Web 服务器上安装 Azure Multi-Factor Authentication 服务器，并按以下过程配置该服务器。

1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“基于窗体”选项卡。
3. 单击“添加...” 按钮。
4. 若要自动检测用户名、密码和域变量，请在“自动配置基于窗体的网站”对话框内输入登录 URL（例如 https://localhost/contoso/auth/login.aspx），然后单击“确定”。
5. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。
6. 如果无法自动检测到页面变量，请单击 “自动配置基于表单的网站”对话框中的“手动指定...”按钮。
7. 在“添加基于窗体的网站”对话框中，将登录页的 URL 输入到“提交 URL”字段中并输入应用程序名称（可选）。 应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。 有关“提交 URL”的详细信息，请参阅帮助文件。
8. 选择正确的请求格式。 对于大多数 Web 应用程序来说，此项设置为“POST 或 GET”。
9. 输入用户名变量、密码变量和域变量（如果它显示在登录页上）。 你可能需要在 Web 浏览器中导航到该登录页，右键单击该页并选择“查看源文件”，以在页中查找输入框的名称。
10. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。 请参阅帮助文件，以了解有关此功能的其他信息。
11. 单击“高级...” 按钮可查看高级设置，这些高级设置提供以下功能：选择自定义拒绝页文件、使用 Cookie 将通过网站成功进行的身份验证缓存一段时间，以及选择如何针对 Windows 域、LDAP 目录或 RADIUS 服务器对主要凭据进行身份验证。 完成后，单击“确定”按钮返回到“添加基于窗体的网站”对话框。 有关高级设置的详细信息，请参阅帮助文件。
12. 单击“确定”按钮。
13. 检测到或输入 URL 和页变量后，网站数据将显示在基于窗体的面板中。
14. 请参阅下面的“为 Azure Multi-Factor Authentication 服务器启用 IIS 插件”部分完成 IIS 身份验证配置。

## <a name="using-integrated-windows-authentication-with-azure-multifactor-authentication-server"></a>将集成 Windows 身份验证用于 Azure Multi-Factor Authentication 服务器
若要保护使用集成式 Windows HTTP 身份验证的 IIS Web 应用程序，请在 IIS Web 服务器上安装 Azure Multi-Factor Authentication 服务器，并按以下过程配置该服务器。

1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“HTTP”选项卡。 单击“基于窗体”选项卡。
3. 单击“添加...” 按钮。
4. 在“添加基 URL”对话框的“基 URL”字段中输入执行 HTTP 身份验证的网站的 URL（例如 http://localhost/owa）并输入应用程序名称（可选）。 应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。
5. 调整空闲超时和会话时间上限（如果默认值不够）。
6. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。 请参阅帮助文件，以了解有关此功能的其他信息。
7. 如果需要，请选中“Cookie 缓存”框。
8. 单击“确定”按钮。
9. 请参阅下面的 [为 Azure Multi-Factor Authentication 服务器启用 IIS 插件](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) 部分完成 IIS 身份验证配置。

## <a name="enable-iis-plugins-for-azure-multifactor-authentication-server"></a>为 Azure Multi-Factor Authentication 服务器启用 IIS 插件
配置基于窗体的身份验证或 HTTP 身份验证 URL 和设置后，必须在 IIS 中选择应加载并启用 Azure Multi-Factor Authentication IIS 插件的位置。 请按以下过程操作：

1. 如果在 IIS 6 上运行，请单击 ISAPI 选项卡并选择在其中运行 Web 应用程序的网站（例如，默认网站），以便为该网站启用 Azure Multi-Factor Authentication ISAPI 筛选器插件。
2. 如果在 IIS 7 或更高版本上运行，请单击“本机模块”选项卡，并选择要在所需级别启用 IIS 插件的服务器、网站或应用程序。
3. 单击屏幕顶部的“启用 IIS 身份验证”框。 现在，Azure Multi-Factor Authentication 将保护所选 IIS 应用程序。 确保已将用户导入该服务器。 如果你想将内部 IP 地址加入允许列表，以便在从这些位置登录网站时不需要双因素身份验证，请参阅下面的“受信任的 IP”部分。

## <a name="trusted-ips"></a>受信任的 IP
受信任的 IP 允许用户跳过对从特定 IP 地址或子网发起的网站请求的 Azure Multi-Factor Authentication。 例如，当用户从办公室登录时，你可能想让这些用户免除 Azure Multi-Factor Authentication。 为此，你可将办公室子网指定为受信任 IP 条目。 若要配置受信任的 IP，请使用以下过程：

1. 在“IIS 身份验证”部分中，单击“受信任 IP”选项卡。
2. 单击“添加...” 按钮。
3. 显示“添加受信任 IP”对话框时，选择“单个 IP”、“IP 范围”或“子网”单选按钮。
4. 输入应加入允许列表的 IP 地址、IP 地址范围或子网。 如果输入子网，请选择适当的子网掩码，然后单击“确定”按钮。 现已添加允许列表。




<!--HONumber=Nov16_HO2-->


