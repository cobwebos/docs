---
title: 在防火墙或代理服务器上将 Azure 门户 URL 加入安全列表
description: 将这些 URL 添加到代理服务器旁路，以便与 Azure 门户及其服务通信
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255041"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>在防火墙或代理服务器上将 Azure 门户 URL 加入安全列表

可以配置本地安全设备，绕过针对 Azure 门户 URL 的安全限制。 此配置可以改进局域网或广域网和 Azure 云之间的性能和连接性。

网络管理员经常会部署代理服务器、防火墙或其他设备。 这些设备有助于确保用户访问 Internet 时的安全性并控制其访问方式。 旨在保护用户的规则有时候可能会阻止合法的与业务相关的 Internet 流量或降低其速度。 该流量包括你与 Azure 之间的通信。 为了优化你的网络与 Azure 门户及其服务之间的连接，建议将 Azure 门户 URL 添加到安全列表。

## <a name="azure-portal-urls-for-proxy-bypass"></a>用于跳过代理的 Azure 门户 URL

Azure 门户的安全列表的 URL 终结点特定于部署组织时所在的 Azure 云。 若要让发往这些终结点的网络流量绕过限制，请选择你的云。 然后，将 URL 列表添加到代理服务器或防火墙。

#### <a name="public-cloud"></a>[公共云](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[美国政府云](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[中国政府云](#tab/china-government-cloud)

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
> 发往这些终结点的流量使用标准的 TCP 端口：80 (HTTP) 和 443 (HTTPS)。
>
>
