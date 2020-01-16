---
title: 远程桌面客户端 Windows 虚拟桌面故障排除-Azure
description: 如何解决在 Windows 虚拟桌面租户环境中设置客户端连接时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bcf22ec1eaf05f5dda6396cca017fa21fd6ddbf5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968180"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>远程桌面客户端疑难解答

本文介绍远程桌面客户端的常见问题以及如何修复这些问题。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>适用于 Windows 7 的远程桌面客户端或 Windows 10 停止响应或无法打开

使用以下 PowerShell cmdlet 清理带外（OOB）客户端注册表。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

导航到 **%AppData%\RdClientRadc**并删除所有内容。

卸载并重新安装适用于 Windows 7 和 Windows 10 的远程桌面客户端。

## <a name="web-client-wont-open"></a>Web 客户端无法打开

首先，通过在浏览器中打开另一个网站来测试 internet 连接;例如， [www.bing.com](https://www.bing.com)。

使用**nslookup**确认 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试与其他客户端（如 Windows 7 或 Windows 10 的远程桌面客户端）进行连接，并检查是否可以打开 web 客户端。

### <a name="opening-another-site-fails"></a>打开另一个站点失败

这通常是由于网络连接问题或网络中断引起的。 建议联系网络支持。

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup 无法解析名称

这通常是由于网络连接问题或网络中断引起的。 建议联系网络支持。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>你的客户端无法连接，但网络上的其他客户端可以连接

如果浏览器在你使用 web 客户端时开始运行或停止工作，请按照以下说明进行操作：

1. 重新启动浏览器。
2. 清除浏览器 cookie。 请参阅[如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅[清除浏览器缓存](https://binged.it/2RKyfdU)。
4. 以专用模式打开浏览器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

尝试使用其他浏览器或客户端进行连接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他浏览器和客户端也不能正常工作或未能打开

如果在切换浏览器后问题仍然存在，则可能是你的浏览器出现问题，但你的网络并不是这样。 建议联系网络支持。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端将保留凭据提示

如果 Web 客户端继续提示输入凭据，请按照以下说明进行操作：

1. 确认 web 客户端 URL 是正确的。
2. 确认你使用的凭据适用于绑定到该 URL 的 Windows 虚拟桌面环境。
3. 清除浏览器 cookie。 有关更多详细信息，请参阅[如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 有关更多详细信息，请参阅为[浏览器清除浏览器缓存](https://binged.it/2RKyfdU)。
5. 以专用模式打开浏览器。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建租户和主机池时排查问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。