---
title: 排查 Azure 中应用程序网关的问题 – ILB ASE | Microsoft Docs
description: 了解如何在 Azure 中使用内部负载均衡器和应用服务环境排查应用程序网关的问题
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: baed2b23a321c53a614303d3085fbb3a4bf6ad0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831089"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>未使用内部负载均衡器和应用服务环境将应用程序网关的后端服务器证书加入允许列表。

本文排查以下问题：在 Azure 中使用端到端 SSL 期间，在后端结合使用内部负载均衡器 (ILB) 和应用服务环境 (ASE) 创建应用程序网关时，证书未加入允许列表。

## <a name="symptoms"></a>症状

在后端使用 ILB 和 ASE 创建应用程序网关时，后端服务器可能不正常。 如果应用程序网关的身份验证证书与后端服务器上配置的证书不匹配，则会出现此问题。 以下面的场景为例：

**应用程序网关配置：**

- **侦听器：** 多站点
- **Port：** 443
- **主机名：** test.appgwtestase.com
- **SSL 证书：** CN=test.appgwtestase.com
- **后端池：** IP 地址或 FQDN
- **IP 地址：** 10.1.5.11
- **HTTP 设置：** HTTPS
- **端口：**:443
- **自定义探测：** 主机名 - test.appgwtestase.com
- **身份验证证书：** test.appgwtestase.com 的 .cer 证书
- **后端运行状况：** 不正常 - 未将应用程序网关的后端服务器证书加入允许列表。

**ASE 配置：**

- **ILB IP：** 10.1.5.11
- **域名：** appgwtestase.com
- **应用服务：** test.appgwtestase.com
- **SSL 绑定：** SNI SSL – CN=test.appgwtestase.com

访问应用程序网关时，会收到以下错误消息，因为后端服务器不正常：

“502 - Web 服务器在充当网关或代理服务器时收到了无效响应”。

## <a name="solution"></a>解决方案

如果不使用主机名来访问 HTTPS 网站，后端服务器将在默认网站上返回配置的证书，以防 SNI 被禁用。 对于 ILB ASE，默认证书来自 ILB 证书。 如果没有为 ILB 配置证书，则证书来自 ASE 应用证书。

如果使用完全限定的域名 (FQDN) 来访问 ILB，则后端服务器将返回 HTTP 设置中上传的正确证书。 如果不是这种情况，请考虑以下选项：

- 使用应用程序网关的后端池中的 FQDN 指向 ILB 的 IP 地址。 仅当已配置专用 DNS 区域或自定义 DNS 时，此选项才可用。 否则，必须为公共 DNS 创建“A”记录。

- 使用 ILB 中上传的证书或 HTTP 设置中的默认证书（ILB 证书）。 应用程序网关在访问用于探测的 ILB IP 时会获取该证书。

- 在 ILB 和后端服务器上使用通配符证书，这样对于所有网站来说，证书都是通用的。 但是，此解决方案仅在子域的情况下才可行，不适用于每个网站都要求不同的主机名这种情形。

- 如果使用的是 ILB 的 IP 地址，请清除应用程序网关的“用于应用服务”选项。

若要降低开销，可在 HTTP 设置中上传 ILB 证书，使探测路径正常工作。 （此步骤仅适用于允许列表操作， 不可用于 SSL 通信。）可通过如下方式检索 ILB 证书：在浏览器中使用 ILB 的 HTTPS IP 地址访问 ILB，然后以 Base-64 编码的 CER 格式导出 SSL 证书，并在相应 HTTP 设置中上传该证书。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
