---
title: 请在防火墙或代理服务器上为 Azure 门户 Url 启用安全
description: 将这些 Url 添加到代理服务器旁路，以与 Azure 门户及其服务进行通信
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900660"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>请在防火墙或代理服务器上为 Azure 门户 Url 启用安全

可以配置本地安全设备，绕过 Azure 门户 Url 的安全限制。 此配置可以提高本地网络或广域网络与 Azure 云之间的性能和连接性。

网络管理员通常会部署代理服务器、防火墙或其他设备。 这些设备有助于保护并控制用户访问 internet 的方式。 旨在保护用户的规则有时会阻止或减慢与合法业务相关的 internet 流量。 此流量包括你和 Azure 之间的通信。 若要优化网络与 Azure 门户及其服务之间的连接，建议将 Azure 门户 Url 添加到安全安全的安全服务。

## <a name="azure-portal-urls-for-proxy-bypass"></a>代理绕过 Azure 门户 Url

要 Azure 门户的安全领域的 URL 终结点特定于部署组织的 Azure 云。 若要允许这些终结点的网络流量绕过限制，请选择云。 然后，将 Url 列表添加到代理服务器或防火墙。

#### <a name="public-cloudtabpublic-cloud"></a>[公有云](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[美国政府云](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[中国政府云](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 到这些终结点的流量对 HTTP （80）和 HTTPS （443）使用标准 TCP 端口。
>
>
## <a name="next-steps"></a>后续步骤

需要为 IP 地址创建安全代码？ 下载适用于你的云的 Microsoft Azure 数据中心 IP 范围的列表：

* [范围](https://www.microsoft.com/download/details.aspx?id=56519)
* [美国政府](https://www.microsoft.com/download/details.aspx?id=57063)
* [德国](https://www.microsoft.com/download/details.aspx?id=57064)
* [中国](https://www.microsoft.com/download/details.aspx?id=57062)

其他 Microsoft 服务使用其他 Url 和 IP 地址进行连接。 若要优化 Microsoft 365 services 的网络连接，请参阅为[Office 365 设置网络](/office365/enterprise/set-up-network-for-office-365)。
