---
title: 排除远程桌面客户端 Windows 虚拟桌面 - Azure
description: 在 Windows 虚拟桌面租户环境中设置客户端连接时，如何解决问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127509"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>排除远程桌面客户端故障

本文介绍了远程桌面客户端的常见问题以及如何解决这些问题。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>适用于 Windows 7 或 Windows 10 的远程桌面客户端停止响应或无法打开

使用以下 PowerShell cmdlet 清理带外 （OOB） 客户端注册表。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

导航到 **%AppData%*RdClientRadc**并删除所有内容。

卸载并重新安装 Windows 7 和 Windows 10 的远程桌面客户端。

## <a name="web-client-wont-open"></a>Web 客户端无法打开

首先，通过在浏览器中打开另一个网站来测试您的互联网连接;例如[，www.bing.com](https://www.bing.com)。

使用**nslookup**确认 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试与其他客户端（如 Windows 7 或 Windows 10 的远程桌面客户端）连接，并检查是否可以打开 Web 客户端。

### <a name="opening-another-site-fails"></a>打开其他站点失败

这通常是由网络连接问题或网络中断引起的。 我们建议您联系网络支持。

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup 无法解析名称

这通常是由网络连接问题或网络中断引起的。 我们建议您联系网络支持。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>客户端无法连接，但网络上的其他客户端可以连接

如果您的浏览器在使用 Web 客户端时开始启动或停止工作，请按照以下说明进行故障排除：

1. 重新启动浏览器。
2. 清除浏览器 Cookie。 请参阅[如何删除互联网资源管理器中的 Cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅[浏览器的透明浏览器缓存](https://binged.it/2RKyfdU)。
4. 在专用模式下打开浏览器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

请尝试使用其他浏览器或客户端进行连接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他浏览器和客户端也出现故障或无法打开

如果问题即使在您切换浏览器后仍然存在，问题可能不是您的浏览器，而是您的网络。 我们建议您联系网络支持。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端不断提示凭据

如果 Web 客户端不断提示凭据，请按照以下说明操作：

1. 确认 Web 客户端 URL 正确。
2. 确认您使用的凭据适用于与 URL 关联的 Windows 虚拟桌面环境。
3. 清除浏览器 Cookie。 有关详细信息，请参阅如何在[Internet 资源管理器中删除 Cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 有关详细信息，请参阅[清除浏览器的浏览器缓存](https://binged.it/2RKyfdU)。
5. 在专用模式下打开浏览器。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要在 Windows 虚拟桌面环境中创建租户和主机池时解决问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决在 Windows 虚拟桌面中使用 PowerShell 时的问题，请参阅[Windows 虚拟桌面电源外壳](troubleshoot-powershell.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。