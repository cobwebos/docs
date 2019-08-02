---
title: Azure 门户 Url 的安全安全 |Microsoft Docs
description: 将这些 Url 添加到代理服务器旁路, 以与 Azure 门户及其服务进行通信
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667475"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>请在防火墙或代理服务器上为 Azure 门户 Url 启用安全

若要在本地或广域网络与 Azure 云之间实现良好的性能和连接性, 请将本地安全设备配置为绕过 Azure 门户 Url 的安全限制。 网络管理员经常部署代理服务器、防火墙或其他设备以帮助保护和控制用户访问 internet 的方式。 但是, 旨在保护用户的规则有时会阻止或减慢与 internet 相关的合法 internet 流量, 包括你和 Azure 之间的通信。 若要优化网络与 Azure 门户及其服务之间的连接, 建议将 Azure 门户 Url 添加到安全安全的安全服务。

## <a name="azure-portal-urls-for-proxy-bypass"></a>代理绕过 Azure 门户 Url

将以下 Url 列表添加到代理服务器或防火墙, 以允许到这些终结点的网络流量绕过限制:

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
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> 到这些终结点的流量对 HTTP (80) 和 HTTPS (443) 使用标准 TCP 端口。
>
>
## <a name="next-steps"></a>后续步骤

* 需要为 IP 地址创建安全代码？ 下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的列表。
* 其他 Microsoft 服务使用其他 Url 和 IP 地址进行连接。 若要优化 Microsoft 365 services 的网络连接, 请参阅为[Office 365 设置网络](/office365/enterprise/set-up-network-for-office-365)。
