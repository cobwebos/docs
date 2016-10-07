<properties 
	pageTitle="RADIUS 身份验证和 Azure Multi-Factor Authentication 服务器"
	description="本 Azure Multi-Factor Authentication 页面将会帮助你部署 RADIUS 身份验证和 Azure Multi-Factor Authentication 服务器。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>



# RADIUS 身份验证和 Azure Multi-Factor Authentication 服务器

使用“RADIUS 身份验证”部分，你可以为 Azure Multi-Factor Authentication 服务器启用和配置 RADIUS 身份验证。RADIUS 是一种标准协议，用于接受身份验证请求并处理这些请求。Azure Multi-Factor Authentication 服务器充当 RADIUS 服务器，并在 RADIUS 客户端（例如 VPN 设备）和身份验证目标（可以是 Active Directory (AD)、LDAP 目录或其他 RADIUS 服务器）之间插入，用于添加 Azure Multi-Factor Authentication。要使 Azure Multi-Factor Authentication起作用，必须将 Azure Multi-Factor Authentication 服务器配置为可以同时与客户端服务器和身份验证目标进行通信。Azure Multi-Factor Authentication 服务器将接受来自 RADIUS 客户端的请求，针对身份验证目标验证凭据，添加 Azure Multi-Factor Authentication，并将响应发送回 RADIUS 客户端。只有主要身份验证和 Azure Multi-Factor Authentication 都成功，整个身份验证才成功。

>[AZURE.NOTE]
MFA 服务器仅支持 PAP（密码身份验证协议），充当 RADIUS 服务器时，还支持 MSCHAPv2（Microsoft 的质询握手身份验证协议）RADIUS 协议。当 MFA 服务器充当另一台 RADIUS 服务器（例如 Microsoft NPS）的代理时，可支持 EAP（可扩展的身份验证协议）等协议，但前提是该 RADIUS 服务器支持该协议。</br>如果在此配置中使用其他协议，单向 SMS 和 OATH 令牌将不起作用，因为 MFA 服务器无法使用该协议发起成功的 RADIUS 质询响应。


![Radius 身份验证](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## LDAP 身份验证配置

若要配置 RADIUS 身份验证，请在 Windows Server 上安装 Azure Multi-Factor Authentication 服务器。如果你有 Active Directory 环境，应将服务器加入到网络内部的域中。使用以下过程可配置 Azure Multi-Factor Authentication 服务器：

1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“RADIUS 身份验证”图标。
2. 选中“启用 RADIUS 身份验证”复选框。
3. 如果 Azure Multi-Factor Authentication RADIUS 服务应绑定到非标准端口以侦听来自将要进行配置的客户端的 RADIUS 请求，请在“客户端”选项卡上更改身份验证端口和记帐端口。
4. 单击“添加...”按钮。
5. 在“添加 RADIUS 客户端”对话框中，输入将向 Azure Multi-Factor Authentication 服务器进行身份验证的设备/服务器的 IP 地址、应用程序名称（可选）和共享机密。Azure Multi-Factor Authentication 服务器和设备/服务器上的共享机密将需要相同。应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。
6. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Multi-Factor Authentication 用户匹配”框。如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。请参阅帮助文件，以了解有关此功能的其他信息。
7. 如果用户将使用 Azure Multi-Factor Authentication移动应用身份验证，并且你要使用 OATH 密码作为带外电话呼叫、短信或推送通知的后备身份验证，请选中“启用后备 OATH 令牌”框。
8. 单击“确定”按钮。
9. 可以重复执行步骤 4 到步骤 8 以添加其他 RADIUS 客户端。
10. 单击“目标”选项卡。
11. 如果 Azure Multi-Factor Authentication 服务器已安装在 Active Directory 环境中已加入域的服务器上，请选择“Windows 域”。
12. 如果应针对 LDAP 目录对用户进行身份验证，请选择“LDAP 绑定”。使用 LDAP 绑定时，必须在“设置”选项卡上单击“目录集成”图标并编辑 LDAP 配置，以便服务器可以绑定到你的目录。可在 LDAP 代理配置指南中找到配置 LDAP 的说明。
13. 如果用户应向其他 RADIUS 服务器进行身份验证，请选择“RADIUS 服务器”。
14. 通过单击“添加...”按钮配置该服务器需要通过代理将 RADIUS 请求发送到的服务器。
15. 在“添加 RADIUS 服务器”对话框中，输入 RADIUS 服务器的 IP 地址和共享机密。Azure Multi-Factor Authentication 服务器和 RADIUS 服务器上的共享机密将需要相同。如果 RADIUS 服务器使用不同端口，请更改身份验证端口和记帐端口。
16. 单击“确定”按钮。
17. 必须在其他 RADIUS 服务器中将 Azure Multi-Factor Authentication 服务器添加为 RADIUS 客户端，以便该 RADIUS 服务器处理 Azure Multi-Factor Authentication 服务器发送给它的访问请求。必须使用 Azure Multi-Factor Authentication 服务器中配置的共享机密。
18. 可以重复执行此步骤以添加其他 RADIUS 服务器，并使用“上移”和“下移”按钮配置该服务器调用这些 RADIUS 服务器时应使用的顺序。这就完成了 Azure Multi-Factor Authentication 服务器配置。该服务器正在已配置端口上侦听来自已配置客户端的 RADIUS 访问请求。


## RADIUS 客户端配置

若要配置 RADIUS 客户端，请遵循以下准则：

- 将你的设备/服务器配置为通过 RADIUS 向 Azure Multi-Factor Authentication 服务器的 IP 地址进行身份验证，后者将充当 RADIUS 服务器。
- 使用上面配置的共享机密。
- 将 RADIUS 超时配置为 30-60 秒，以便有时间验证用户的凭据、执行 Multi-Factor Authentication、接收其响应，然后对 RADIUS 访问请求做出响应。

<!---HONumber=AcomDC_0921_2016-->