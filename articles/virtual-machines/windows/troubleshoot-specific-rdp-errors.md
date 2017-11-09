---
title: "Azure VM 的特定 RDP 错误消息 | Microsoft Docs"
description: "了解在尝试使用远程桌面与 Azure 中 Windows 虚拟机的连接时收到的特定错误消息"
keywords: "远程桌面错误,远程桌面连接错误,无法连接到 VM,远程桌面故障排除"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 8eb4420978c0a66012f3f46d4a5f7ed0da56a3b7
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Azure 中 Windows VM 特定 RDP 错误消息故障排除
在使用远程桌面与 Azure 中 Windows 虚拟机 (VM) 的连接时，可能会收到特定错误消息。 本文详细介绍了一些遇到的更常见错误消息以及解决错误的故障排除步骤。 如果在使用 RDP 连接到 VM 时出现问题，但没有收到特定错误消息，请参阅[远程桌面故障排除指南](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

有关特定错误消息的信息，请参阅以下内容：

* [由于没有可用于提供许可证的远程桌面许可证服务器，远程会话已断开连接](#rdplicense)。
* [远程桌面找不到计算机“名称”](#rdpname)。
* [身份验证出错。无法联系本地安全机构](#rdpauth)。
* [Windows 安全性错误：凭据无效](#wincred)。
* [此计算机无法连接到远程计算机](#rdpconnect)。

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>由于没有可用于提供许可证的远程桌面许可证服务器，远程会话已断开连接。
原因：用于远程桌面服务器角色的 120 天许可宽限期已过期，需要安装许可证。

解决方法是，从门户保存 RDP 文件的本地副本，并在 PowerShell 命令提示符下运行此命令以进行连接。 此步骤仅禁用该连接的许可：

        mstsc <File name>.RDP /admin

如果实际上不需要两个以上同时与 VM 的远程桌面连接，可以使用服务器管理器删除远程桌面服务器角色。

有关详细信息，请参阅博客文章 [Azure VM 失败并出现“没有可用的远程桌面许可证服务器”](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)。

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>远程桌面找不到计算机“名称”。
原因：计算机的远程桌面客户端无法解析 RDP 文件设置中的计算机名称。

可能的解决方法：

* 如果使用组织的 Intranet，请确保计算机可以访问代理服务器，并可以向其发送 HTTPS 流量。
* 如果使用本地存储的 RDP 文件，请尝试使用门户生成的 RDP 文件。 此步骤确保使用虚拟机或云服务的正确 DNS 名称和 VM 的终结点端口。 以下是门户生成的 RDP 文件示例：
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

此 RDP 文件的地址部分包含：

* 包含 VM 的云服务的完全限定域名（在本例中为“tailspin-azdatatier.cloudapp.net”）。
* 远程桌面流量终结点的外部 TCP 端口 (55919)。

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>发生身份验证错误。 无法联系本地安全机构。
原因：目标 VM 在凭据的用户名部分找不到安全机构。

如果用户名格式为 *SecurityAuthority*\\*UserName*（例如：CORP\User1），则 *SecurityAuthority* 部分是 VM 的计算机名（表示本地安全机构）或 Active Directory 域名。

可能的解决方法：

* 如果帐户在本地 VM 上，请确保 VM 名称拼写正确。
* 如果帐户在 Active Directory 域上，请检查域名拼写是否正确。
* 如果帐户是 Active Directory 域帐户且域名拼写正确，请验证域控制器在该域中是否可用。 此问题在包含域控制器的 Azure 虚拟网络中很常见，域控制器由于未启动而无法使用。 解决方法是，可以使用本地管理员帐户而不是域帐户。

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows 安全性错误：凭据无效。
原因：目标 VM 无法验证帐户名和密码。

基于 Windows 的计算机可以验证本地帐户或域帐户的凭据。

* 对于本地帐户，请使用 *ComputerName*\\*UserName* 语法（例如：SQL1\Admin4798）。
* 对于域帐户，请使用 *DomainName*\\*UserName* 语法（例如：CONTOSO\peterodmane）。

如果已将 VM 提升为新的 Active Directory 林中的域控制器，则会将用于登录的本地管理员帐户转换为新的林和域中具有相同密码的等效帐户。 然后，将删除本地帐户。

例如，如果使用本地帐户 DC1\DCAdmin 登录并将虚拟机提升为 corp.contoso.com 域的新林中的域控制器，则将删除 DC1\DCAdmin 本地帐户，并使用同一密码创建新的域帐户 (CORP\DCAdmin)。

请确保帐户名称是虚拟机可以验证为有效帐户的名称，并且密码正确。

如果需要更改本地管理员帐户的密码，请参阅[如何为 Windows 虚拟机重置密码或远程桌面服务](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>此计算机无法连接到远程计算机。
原因：用于连接的帐户没有远程桌面登录权限。

每台 Windows 计算机都具有远程桌面用户本地组，包含可以远程登录的帐户和组。 本地 Administrators 组的成员也具有访问权限，即使在远程桌面用户本地组中未列出这些帐户。 对于已加入域的计算机，本地 Administrators 组还包含该域的域管理员。

确保用于连接的帐户具有远程桌面登录权限。 解决方法是使用域管理员或本地管理员帐户通过远程桌面建立连接。 若要将所需帐户添加到远程桌面用户本地组，请使用 Microsoft 管理控制台管理单元（“系统工具”>“本地用户和组”>“组”>“远程桌面用户”）。

## <a name="next-steps"></a>后续步骤
如果没有发生这些错误，但在使用 RDP 连接时出现未知问题，请参阅[远程桌面故障排除指南](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

* 有关用于访问 VM 上运行的应用程序的故障排除步骤，请参阅[对在 Azure VM 上运行的应用程序的访问进行故障排除](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 如果在 Azure 中使用 Secure Shell (SSH) 连接到 Linux VM 时遇到问题，请参阅[对 Azure 中到 Linux VM 的 SSH 连接进行故障排除](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

