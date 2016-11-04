---
title: 将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用来保护云和本地资源
description: 这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与 AD FS 2.0 配合使用。
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# 将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用来保护云和本地资源
如果你的组织已与 Azure Active Directory 联合，并且你在本地或云中拥有要保护的资源，则可以通过使用 Azure Multi-Factor Authentication 服务器并将其配置为使用 AD FS（以便为高价值终结点触发 Multi-Factor Authentication）来保护这些资源。

本文介绍如何将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用。有关有关配合 Windows Serve 2012 R2 AD FS 使用 Azure Multi-Factor Authentication 的信息，请参阅[将 Azure Multi-Factor Authentication 服务器与 Windows Serve 2012 R2 AD FS 配合使用来保护云和本地资源](multi-factor-authentication-get-started-adfs-w2k12.md)。

## AD FS 2.0 代理
若要使用代理保护 AD FS 2.0，请在 ADFS 代理服务器上安装 Azure Multi-Factor Authentication 服务器，并按以下步骤配置该服务器。

### 使用代理保护 AD FS 2.0
1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“基于窗体”选项卡。
3. 单击“添加...”按钮。
   <center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. 若要自动检测用户名、密码和域变量，请在“自动配置基于窗体的网站”对话框内输入登录 URL（例如 https://sso.contoso.com/adfs/ls），然后单击“确定”。
5. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。请参阅帮助文件，以了解有关此功能的其他信息。
6. 如果无法自动检测到页变量，请单击“自动配置基于窗体的网站”对话框中的“手动指定...”按钮。
7. 在“添加基于窗体的网站”对话框中，将 ADFS 登录页的 URL（例如 https://sso.contoso.com/adfs/ls）输入到“提交 URL”字段中并输入应用程序名称（可选）。应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。有关“提交 URL”的详细信息，请参阅帮助文件。
8. 将请求格式设置为“POST 或 GET”。
9. 输入用户名变量 (ctl00$ContentPlaceHolder1$UsernameTextBox) 和密码变量 (ctl00$ContentPlaceHolder1$PasswordTextBox)。如果基于窗体的登录页显示域文本框，则也输入域变量。你可能需要在 Web 浏览器中导航到该登录页，右键单击该页并选择“查看源文件”，以在登录页中查找输入框的名称。
10. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。
    <center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. 单击“高级...”按钮可查看高级设置，这些高级设置提供以下功能：选择自定义拒绝页文件、使用 Cookie 将通过网站成功进行的身份验证缓存一段时间，以及选择如何对主要凭据进行身份验证。
12. 由于不可能将 ADFS 代理服务器加入到域，因此，你可能需要使用 LDAP 连接到域控制器来进行用户导入和预身份验证。在“基于高级窗体的网站”对话框中，单击“主要身份验证”选项卡并为“预身份验证”身份验证类型选择“LDAP 绑定”。
13. 完成后，单击“确定”按钮返回到“添加基于窗体的网站”对话框。有关高级设置的详细信息，请参阅帮助文件。
14. 单击“确定”按钮关闭对话框。
15. 检测到或输入 URL 和页变量后，网站数据将显示在基于窗体的面板中。
16. 单击“本机模块”选项卡，然后选择服务器、在其下运行 ADFS 代理的网站（例如“默认网站”）或 ADFS 代理应用程序（例如“adfs”下的“ls”），以便在所需的级别启用 IIS 插件。
17. 单击屏幕顶部的“启用 IIS 身份验证”框。
18. 此时已启用 IIS 身份验证。但是，为了通过 LDAP 向 Active Directory (AD) 进行预身份验证，必须配置与域控制器的 LDAP 连接。为此，请单击“目录集成”图标。
19. 在“设置”选项卡上，选择“使用特定 LDAP 配置”单选按钮。
    <center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. 单击“编辑...”按钮。
21. 在“编辑 LDAP 配置”对话框中，使用连接到 AD 域控制器时所需的信息填充字段。下表中提供了字段的说明。注意：Azure Multi-Factor Authentication 服务器帮助文件中也提供了此信息。
22. 通过单击“测试”按钮来测试 LDAP 连接。
    <center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. 如果 LDAP 连接测试成功，请单击“确定”按钮。
24. 接下来，单击“公司设置”图标，然后选择“用户名解析”选项卡。
25. 选择“对匹配的用户名使用 LDAP 唯一标识符特性”单选按钮。
26. 如果用户将以“域\\用户名”格式在“ADFS 代理登录”窗体中输入其用户名，服务器在创建 LDAP 查询时需要能够将域与用户名剥离。可以通过注册表设置完成此操作。
27. 在 64 位服务器上，打开注册表编辑器，并转到 HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor。如果在 32 位服务器上，请从路径中除去“Wow6432Node”。创建名为“UsernameCxz\_stripPrefixDomain”的新 DWORD 注册表项，将其值设为 1。现在 Azure Multi-Factor Authentication 将保护 ADFS 代理。确保已将 Active Directory 中的用户导入服务器。如果你想将内部 IP 地址加入允许列表，以便在从这些位置登录网站时不需要双因素身份验证，请参阅下面的“受信任的 IP”部分。

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## 不带代理的 AD FS 2.0 直通
若要在未使用 ADFS 代理时保护 ADFS，请在 ADFS 服务器上安装 Azure Multi-Factor Authentication 服务器，并按以下步骤配置该服务器。

### 不使用代理保护 AD FS 2.0
1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“HTTP”选项卡。
3. 单击“添加...”按钮。
4. 在“添加基 URL”对话框的“基 URL”字段中输入执行 HTTP 身份验证的 ADFS 网站的 URL（例如 https://sso.domain.com/adfs/ls/auth/integrated）并输入应用程序名称（可选）。应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。
5. 如果需要，请调整空闲超时和会话时间上限。
6. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。请参阅帮助文件，以了解有关此功能的其他信息。
7. 如果需要，请选中“Cookie 缓存”框。
   <center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. 单击“确定”按钮。
9. 单击“本机模块”选项卡，然后选择服务器、在其下运行 ADFS 的网站（例如“默认网站”）或 ADFS 应用程序（例如“adfs”下的“ls”），以便在所需的级别启用 IIS 插件。
10. 单击屏幕顶部的“启用 IIS 身份验证”框。现在，Azure Multi-Factor Authentication 将保护 ADFS。确保已将 Active Directory 中的用户导入服务器。如果你想将内部 IP 地址加入允许列表，以便在从这些位置登录网站时不需要双因素身份验证，请参阅下面的“受信任的 IP”部分。

## 受信任的 IP
受信任的 IP 允许用户跳过对从特定 IP 地址或子网发起的网站请求的 Azure Multi-Factor Authentication。例如，当用户从办公室登录时，你可能想让这些用户免除 Azure Multi-Factor Authentication。为此，你可将办公室子网指定为受信任 IP 条目。

### 配置受信任的 IP
1. 在“IIS 身份验证”部分中，单击“受信任 IP”选项卡。
2. 单击“添加...”按钮。
3. 显示“添加受信任 IP”对话框时，选择“单个 IP”、“IP 范围”或“子网”单选按钮。
4. 输入应加入允许列表的 IP 地址、IP 地址范围或子网。如果输入子网，请选择适当的子网掩码，然后单击“确定”按钮。现已添加受信任的 IP。

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=AcomDC_0921_2016-->