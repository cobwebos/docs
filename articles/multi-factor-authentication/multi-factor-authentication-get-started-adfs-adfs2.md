---
title: "将 Azure MFA 服务器与 AD FS 2.0 配合使用 | Microsoft Docs"
description: "这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何将 Azure MFA 与 AD FS 2.0 配合使用。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 96168849-241a-4499-a224-d829913caa7e
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 639f11856fcb0828db2375e428d9f7efe640b5d7


---
# <a name="secure-cloud-and-onpremises-resources-using-azure-multifactor-authentication-server-with-ad-fs-20"></a>将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用来保护云和本地资源
本文适用于联合使用 Azure Active Directory 以及希望保护本地或云中资源的组织。 通过使用 Azure 多重身份验证服务器，并将其配置为使用 AD FS，为重要的终结点触发双重验证，从而保护资源。

本文介绍如何将 Azure Multi-Factor Authentication 服务器与 AD FS 2.0 配合使用。  获取有关[将 Azure 多重身份验证服务器与 Windows Server 2012 R2 AD FS 配合使用来保护云和本地资源](multi-factor-authentication-get-started-adfs-w2k12.md)的详细信息。

## <a name="secure-ad-fs-20-with-a-proxy"></a>使用代理保护 AD FS 2.0
若要使用代理保护 AD FS 2.0，请在 ADFS 代理服务器上安装 Azure 多重身份验证服务器并配置该服务器。

### <a name="configure-iis-authentication"></a>配置 IIS 身份验证
1. 在 Azure 多重身份验证服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“基于表单”选项卡。
3. 单击“添加...” 按钮。
   <center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. 若要自动检测用户名、密码和域变量，请在“自动配置基于表单的网站”对话框内输入登录 URL（例如 https://sso.contoso.com/adfs/ls），然后单击“确定”。
5. 如果所有用户均已导入或将导入到该服务器并接受双重验证，请选中“需要 Azure 多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除进行多重身份验证，请使该框处于未选中状态。 请参阅帮助文件，了解有关此功能的其他信息。
6. 如果无法自动检测到页面变量，请在“自动配置基于表单的网站”对话框中 “自动配置基于表单的网站”对话框中的“手动指定...”按钮。
7. 在“添加基于表单的网站”对话框中，在“提交 URL”字段中输入 ADFS 登录页面的 URL（例如 https://sso.contoso.com/adfs/ls），并输入应用程序名称（可选）。 应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。 有关“提交 URL”的详细信息，请参阅帮助文件。
8. 将请求格式设置为“POST 或 GET”。
9. 输入用户名变量 (ctl00$ContentPlaceHolder1$UsernameTextBox) 和密码变量 (ctl00$ContentPlaceHolder1$PasswordTextBox)。 如果基于窗体的登录页显示域文本框，则也输入域变量。 可能需要在 Web 浏览器中导航到该登录页，右键单击该页并选择“查看源”，以在登录页中查找输入框的名称。
10. 如果所有用户均已导入或将导入到该服务器并接受双重验证，请选中“需要 Azure 多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除进行多重身份验证，请使该框处于未选中状态。
    <center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. 单击“高级...” 按钮可查看高级设置。 可以配置以下设置：选择自定义拒绝页文件、使用 Cookie 将通过网站成功进行的身份验证缓存一段时间，以及选择如何对主要凭据进行身份验证。
12. 由于不可能将 ADFS 代理服务器加入到域，因此，可以使用 LDAP 连接到域控制器来进行用户导入和预身份验证。 在“基于高级表单的网站”对话框中，单击“主要身份验证”选项卡并为“预身份验证”身份验证类型选择“LDAP 绑定”。
13. 完成后，单击“确定”按钮返回到“添加基于表单的网站”对话框。 有关高级设置的详细信息，请参阅帮助文件。
14. 单击“确定”按钮关闭对话框。
15. 检测到或输入 URL 和页面变量后，网站数据将显示在基于表单的面板中。
16. 单击“本机模块”选项卡，然后选择服务器、在其下运行 ADFS 代理的网站（例如“默认网站”）或 ADFS 代理应用程序（例如“adfs”下的“ls”），以在所需的级别启用 IIS 插件。
17. 单击屏幕顶部的“启用 IIS 身份验证”框。
18. 此时已启用 IIS 身份验证。

### <a name="configure-directory-integration"></a>配置目录集成
尽管已启用了 IIS 身份验证，但为了通过 LDAP 向 Active Directory (AD) 进行预身份验证，还必须配置到域控制器的 LDAP 连接。

1. 单击“目录集成”图标。
2. 在“设置”选项卡上，选择“使用特定 LDAP 配置”单选按钮。
   <center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. 单击“编辑...” 按钮。
4. 在“编辑 LDAP 配置”对话框中，使用连接到 AD 域控制器时所需的信息填充字段。 下表中提供了字段的说明。 Azure 多重身份验证服务器帮助文件中也提供了此信息。
5. 通过单击“测试”按钮来测试 LDAP 连接。
   <center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. 如果 LDAP 连接测试成功，请单击“确定”按钮。

### <a name="configure-company-settings"></a>配置公司设置
1. 接下来，单击“公司设置”图标，然后选择“用户名解析”选项卡。
2. 选择“使用 LDAP 唯一标识符属性匹配用户名”单选按钮。
3. 如果用户以“域\用户名”格式输入用户名，服务器在创建 LDAP 查询时则需要能够将域与用户名相剥离。 可以通过注册表设置完成此操作。
4. 在 64 位服务器上，打开注册表编辑器，并转到 HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor。 如果在 32 位服务器上，请从路径中除去“Wow6432Node”。 创建名为“UsernameCxz_stripPrefixDomain”的 DWORD 注册表项，将值设置为 1。 现在 Azure Multi-Factor Authentication 将保护 ADFS 代理。

确保已将 Active Directory 中的用户导入服务器。 如果想将内部 IP 地址加入白名单，以在从这些位置登录网站时不需要进行双重验证，请参阅以下[受信任的 IP部分](#trusted-ips)。

<center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>不带代理的 AD FS 2.0 直通
可以在没有使用 AD FS 代理时保护 AD FS。 请在 AD FS 服务器上安装 Azure 多重身份验证服务器，并按以下步骤配置该服务器：

1. 在 Azure 多重身份验证服务器内，单击左侧菜单中的“IIS 身份验证”图标。
2. 单击“HTTP”选项卡。
3. 单击“添加...” 按钮。
4. 在“添加基 URL”对话框的“基 URL”字段中输入进行 HTTP 身份验证的 ADFS 网站的 URL（例如 https://sso.domain.com/adfs/ls/auth/integrated）。 然后，输入应用程序名称（可选）。 应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。
5. 如果需要，请调整空闲超时和会话时间上限。
6. 如果所有用户均已导入或将导入到该服务器并接受双重验证，请选中“需要 Azure 多重身份验证用户匹配”框。 如果大量用户尚未导入到该服务器并且/或者将免除进行多重身份验证，请使该框处于未选中状态。 请参阅帮助文件，了解有关此功能的其他信息。
7. 如果需要，请选中“Cookie 缓存”框。
   <center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. 单击“确定”按钮。
9. 单击“本机模块”选项卡，然后选择服务器、网站（例如“默认网站”）或 ADFS 应用程序（例如“adfs”下的“ls”），以在所需的级别启用 IIS 插件。
10. 单击屏幕顶部的“启用 IIS 身份验证”框。 现在，Azure Multi-Factor Authentication 将保护 ADFS。

确保已将 Active Directory 中的用户导入服务器。 如果想将内部 IP 地址加入白名单，以在从这些位置登录网站时不需要双重验证，请参阅以下“受信任的 IP”部分。

## <a name="trusted-ips"></a>受信任的 IP
受信任的 IP 允许用户跳过对从特定 IP 地址或子网发起的网站请求的 Azure 多重身份验证。 例如，可能希望当用户在办公室进行登录时，无需进行双重验证。 为此，可将办公室子网指定为受信任的 IP 条目。

### <a name="to-configure-trusted-ips"></a>配置受信任的 IP
1. 在“IIS 身份验证”部分中，单击“受信任的 IP”选项卡。
2. 单击“添加...” 按钮。
3. 显示“添加受信任的 IP”对话框时，选择“单个 IP”、“IP 范围”或“子网”其中一个单选按钮。
4. 输入应加入白名单的 IP 地址、IP 地址范围或子网。 如果输入子网，请选择适当的网络掩码，然后单击“确定”按钮。 现已添加受信任的 IP。

<center>![设置](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Nov16_HO2-->


