---
title: LDAP 身份验证和 Azure Multi-Factor Authentication 服务器
description: 本 Azure Multi-Factor Authentication 页面将会帮助你部署 LDAP 身份验证和 Azure Multi-Factor Authentication 服务器。
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# LDAP 身份验证和 Azure Multi-Factor Authentication 服务器
默认情况下，Azure Multi-Factor Authentication 服务器配置为从 Active Directory 导入或同步用户。但是，可以将它配置为绑定到不同 LDAP 目录，如 ADAM 目录或特定的 Active Directory 域控制器。配置为通过 LDAP 连接到目录时，Azure Multi-Factor Authentication 服务器可以配置为充当 LDAP 代理来执行身份验证。它还允许将 LDAP 绑定用作 RADIUS 目标，使用 IIS 身份验证时对用户进行预身份验证，或者在 Azure Multi-Factor Authentication 用户门户中进行主要身份验证。

使用 Azure Multi-Factor Authentication作为 LDAP 代理时，将在 LDAP 客户端（例如 VPN 设备、应用程序）与 LDAP 目录服务器之间插入 Azure Multi-Factor Authentication 服务器，以便添加 Multi-Factor Authentication。要使 Azure Multi-Factor Authentication 起作用，必须将 Azure Multi-Factor Authentication 服务器配置为同时与客户端服务器和 LDAP 目录进行通信。在此配置中，Azure Multi-Factor Authentication 服务器接受来自客户端服务器和应用程序的 LDAP 请求，并将这些请求转发到目标 LDAP 目录服务器以验证主要凭据。如果 LDAP 目录的响应显示主要凭据有效，Azure Multi-Factor Authentication 将执行双重身份验证，并将响应发送回 LDAP 客户端。只有向 LDAP 服务器进行的身份验证和 Multi-Factor Authentication 都成功，整个身份验证才成功。

## LDAP 身份验证配置
若要配置 LDAP 身份验证，请在 Windows Server 上安装 Azure Multi-Factor Authentication 服务器。请按以下过程操作：

1. 在 Azure Multi-Factor Authentication 服务器内，单击左侧菜单中的“LDAP 身份验证”图标。
2. 选中“启用 LDAP 身份验证”复选框。![LDAP 身份验证](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. 如果 Azure Multi-Factor Authentication LDAP 服务应绑定到非标准端口以侦听来自将要进行配置的客户端的 LDAP 请求，请在“客户端”选项卡上更改 TCP 端口和 SSL 端口。
4. 如果你计划从客户端到 Azure Multi-Factor Authentication 服务器使用 LDAPS，则必须在运行该服务器的服务器上安装 SSL 证书。单击“SSL 证书”框旁边的“浏览...”按钮，然后选择将用于安全连接的已安装证书。
5. 单击“添加...”按钮。
6. 在“添加 LDAP 客户端”对话框中，输入将向该服务器进行身份验证的设备、服务器或应用程序的 IP 地址以及应用程序名称（可选）。应用程序名称将出现在 Azure Multi-Factor Authentication 报告中，并可能会显示在短信或移动应用身份验证消息中。
7. 如果所有用户均已导入或将导入到该服务器并接受 Multi-Factor Authentication，请选中“需要 Azure Multi-Factor Authentication 用户匹配”框。如果大量用户尚未导入到该服务器并且/或者将免除 Multi-Factor Authentication，请使该框处于未选中状态。请参阅帮助文件，以了解有关此功能的其他信息。
8. 可以重复执行步骤 5 到步骤 7 以添加其他 LDAP 客户端。
9. 当 Azure Multi-Factor Authentication配置为接收 LDAP 身份验证时，它必须将这些身份验证通过代理发送到 LDAP 目录。因此，“目标”选项卡只显示一个使用 LDAP 目标的灰显选项。若要配置 LDAP 目录连接，请单击“目录集成”图标。
10. 在“设置”选项卡上，选择“使用特定 LDAP 配置”单选按钮。
11. 单击“编辑...”按钮。
12. 在“编辑 LDAP 配置”对话框中，使用连接到 LDAP 目录时所需的信息填充字段。下表中提供了字段的说明。注意：Azure Multi-Factor Authentication 服务器帮助文件中也提供了此信息。![目录集成](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. 通过单击“测试”按钮来测试 LDAP 连接。
14. 如果 LDAP 连接测试成功，请单击“确定”按钮。
15. 单击“筛选器”选项卡。该服务器已预配置为从 Active Directory 加载容器、安全组和用户。如果要绑定到其他 LDAP 目录，你可能需要编辑显示的筛选器。有关筛选器的详细信息，请单击“帮助”链接。
16. 单击“属性”选项卡。该服务器已预配置为从 Active Directory 映射属性。
17. 如果要绑定到其他 LDAP 目录或更改预配置的属性映射，请单击“编辑...”按钮。
18. 在“编辑属性”对话框中，修改你的目录的 LDAP 属性映射。可以通过单击每个字段旁边的“...”按钮来键入或选择属性名称。
19. 有关属性的详细信息，请单击“帮助”链接。
20. 单击“确定”按钮。
21. 单击“公司设置”图标，然后选择“用户名解析”选项卡。22. 如果从已加入域的服务器连接到 Active Directory，你应该能够让“使用 Windows 安全标识符 (SID) 匹配用户名”单选按钮处于选中状态。否则，请选中“使用 LDAP 唯一标识符属性匹配用户名”单选按钮。选中此项时，Azure Multi-Factor Authentication 服务器会尝试将每个用户名解析为 LDAP 目录中的唯一标识符。将对“目录集成 -> 属性”选项卡中定义的用户名属性执行 LDAP 搜索。当用户进行身份验证时，会将用户名解析为 LDAP 目录中的唯一标识符，并将使用该唯一标识符来匹配 Azure Multi-Factor Authentication 数据文件中的用户。这允许不区分大小写的比较以及长、短用户名格式。这将完成 Azure Multi-Factor Authentication 服务器配置。服务器现在正在配置的端口上侦听来自配置的客户端的 LDAP 访问请求，并将设置为将这些请求通过代理发送到 LDAP 目录进行身份验证。

## LDAP 客户端配置
若要配置 LDAP 客户端，请遵循以下准则：

* 将你的设备、服务器或应用程序配置为通过 LDAP 向 Azure Multi-Factor Authentication 服务器进行身份验证，就像它是你的 LDAP 目录一样。应使用通常用于直接连接到你的 LDAP 目录的设置（服务器名称或 IP 地址除外，应使用 Azure Multi-Factor Authentication 服务器的服务器名称或 IP 地址）。
* 将 LDAP 超时配置为 30-60 秒，以便有时间使用 LDAP 目录验证用户的凭据、执行第二重身份验证、接收其响应，然后对 LDAP 访问请求做出响应。
* 如果使用 LDAPS，进行 LDAP 查询的设备或服务器必须信任 Azure Multi-Factor Authentication 服务器上安装的 SSL 证书。

<!---HONumber=AcomDC_0921_2016-->