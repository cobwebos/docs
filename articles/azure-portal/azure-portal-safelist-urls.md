---
title: 在 Azure 门户安全列表 Url |Microsoft Docs
description: 将这些 Url 添加到代理服务器绕过与在 Azure 门户和其服务进行通信
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305061"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>在 Azure 门户安全列表防火墙或代理服务器上的 Url

对于良好的性能和在本地或广域网络和 Azure 云之间的连接，配置的本地安全设备来绕过安全限制适用于 Azure 门户的 Url。 网络管理员通常会部署代理服务器、 防火墙或其他设备来帮助保护并让用户如何访问 internet 的控制。 但是，规则旨在保护用户有时可以阻止或减慢合法业务相关 internet 通信，包括您与 Azure 之间的通信。 若要优化你的网络和 Azure 门户和服务之间的连接，我们建议添加 Azure 门户为你的安全列表的 Url。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 门户的代理 Url 绕过

将以下 Url 列表添加到你的代理服务器或防火墙以允许发往这些终结点绕过限制网络流量：

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> 与这些终结点的流量使用 HTTP (80) 和 HTTPS (443) 的标准 TCP 端口。
>
>
## <a name="next-steps"></a>后续步骤

* 需要到安全列表的 IP 地址？ 下载的列表[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
* 其他 Microsoft 服务使用其他 Url 和 IP 地址进行连接。 若要优化 Microsoft 365 服务的网络连接，请参阅[为 Office 365 设置您的网络](/office365/enterprise/set-up-network-for-office-365)。
