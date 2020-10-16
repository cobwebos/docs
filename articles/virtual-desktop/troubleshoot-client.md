---
title: 远程桌面客户端 Windows 虚拟桌面故障排除-Azure
description: 如何解决在 Windows 虚拟桌面租户环境中设置客户端连接时遇到的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6083dbcc270c0e9dde1da45ed01369d03146237
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108960"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>远程桌面客户端疑难解答

本文介绍远程桌面客户端的常见问题以及如何修复这些问题。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>适用于 Windows 7 的远程桌面客户端或 Windows 10 停止响应或无法打开

从版本1.2.790 开始，可以重置 "关于" 页中的用户数据或使用命令。

使用以下命令从所有工作区中删除用户数据、还原默认设置和取消订阅。

```cmd
msrdcw.exe /reset [/f]
```

如果你使用的是早期版本的远程桌面客户端，则建议你卸载并重新安装客户端。

## <a name="web-client-wont-open"></a>Web 客户端无法打开

首先，通过在浏览器中打开另一个网站来测试 internet 连接;例如， [www.bing.com](https://www.bing.com)。

使用 **nslookup** 确认 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试与其他客户端（如 Windows 7 或 Windows 10 的远程桌面客户端）进行连接，并检查是否可以打开 web 客户端。

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>连接到 web 客户端时无法打开其他网站

如果在连接到 web 客户端时无法打开其他网站，则可能是网络连接问题或网络中断。 建议联系网络支持。

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup 无法解析名称

如果 nslookup 无法解析该名称，则可能是网络连接问题或网络中断。 建议联系网络支持。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>你的客户端无法连接，但网络上的其他客户端可以连接

如果浏览器在你使用 web 客户端时开始运行或停止工作，请按照以下说明进行操作：

1. 重新启动浏览器。
2. 清除浏览器 cookie。 请参阅 [如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅 [清除浏览器缓存](https://binged.it/2RKyfdU)。
4. 以专用模式打开浏览器。

## <a name="client-doesnt-show-my-resources"></a>客户端不显示我的资源

首先，请检查正在使用的 Azure Active Directory 帐户。 如果已使用与要用于 Windows 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

如果使用的是 Windows 虚拟桌面 (经典) ，请使用 [本文](./virtual-desktop-fall-2019/connect-web-2019.md) 中的 web 客户端链接连接到资源。

如果这不起作用，请确保应用组与工作区关联。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

尝试使用其他浏览器或客户端进行连接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他浏览器和客户端也不能正常工作或未能打开

如果在切换浏览器后问题仍然存在，则可能是你的浏览器出现问题，但你的网络并不是这样。 建议联系网络支持。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端将保留凭据提示

如果 Web 客户端继续提示输入凭据，请按照以下说明进行操作：

1. 确认 web 客户端 URL 是正确的。
2. 确认你使用的凭据适用于绑定到该 URL 的 Windows 虚拟桌面环境。
3. 清除浏览器 cookie。 有关详细信息，请参阅 [如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 有关详细信息，请参阅为 [浏览器清除浏览器缓存](https://binged.it/2RKyfdU)。
5. 以专用模式打开浏览器。

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows 客户端阻止 Windows 虚拟桌面 (经典) 源

如果 Windows 客户端源不会显示 Windows 虚拟桌面 (经典) 应用，请按照以下说明进行操作：

1. 检查条件性访问策略是否包括与 Windows 虚拟桌面 (经典) 相关联的应用程序 Id。
2. 检查条件访问策略是否阻止除 Windows 虚拟桌面 (经典) 应用 Id 之外的所有访问。 如果是这样，则需要将应用 ID **9cdead84-a844-4324-93f2-b2e6bb768d07** 添加到策略，以允许客户端发现源。

如果在列表中找不到应用 ID 9cdead84-a844-4324-93f2-b2e6bb768d07，则需要注册 Windows 虚拟桌面资源提供程序。 若要注册资源提供程序：

1. 登录到 Azure 门户。
2. 中转到 " **订阅**"，然后选择订阅。
3. 在页面左侧的菜单中，选择 " **资源提供程序**"。
4. 找到并选择 **DesktopVirtualization**，然后选择 " **重新注册**"。

## <a name="next-steps"></a>后续步骤

- 如需简要了解如何排查 Windows 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查在 Windows 虚拟桌面环境中创建 Windows 虚拟桌面环境和主机池时遇到的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Windows 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅 [Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
