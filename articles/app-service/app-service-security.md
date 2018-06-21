---
title: Azure 应用服务和 Azure Functions 中的安全性 | Microsoft Docs
description: 了解应用服务如何帮助保护应用，以及如何进一步锁定应用使其免受威胁。
keywords: azure 应用服务, web 应用, 移动应用, api 应用, 函数应用, 安全性, 保护, 受保护, 符合性, 符合, 证书, 证书, https, ftps, tls, 信任, 加密, 加密, 已加密, ip 限制, 身份验证, 授权, 身份验证, 授权, msi, 托管服务标识, 密钥, 密钥, 修补, 修补程序, 修补程序, 版本, 隔离, 网络隔离, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: cephalin
ms.openlocfilehash: 2ca1c1518589e60a03570e1c2063381f749ed9aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658917"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Azure 应用服务和 Azure Functions 中的安全性

本文介绍 [Azure 应用服务](app-service-web-overview.md)如何帮助保护 Web 应用、移动应用后端、API 应用和 [Azure Functions](/azure/azure-functions/)。 它还介绍如何使用内置的应用服务功能进一步保护应用。

应用服务的各个平台组件（包括 Azure VM、存储、网络连接、Web 框架、管理和集成功能）都得到了积极保护和加强。 应用服务持续进行严格的符合性检查，以确保：

- 你的应用资源与其他客户的 Azure 资源隔离开来，从而[受到保护](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)。
- [定期更新 VM 实例和运行时软件](app-service-patch-os-runtime.md)，以解决新发现的漏洞。 
- 你的应用与其他 Azure 资源（例如 [SQL 数据库](/services/sql-database/)）之间的密钥（例如连接字符串）通信一直在 Azure 中进行，不跨越任何网络边界。 存储密钥时始终对其加密。
- 通过应用服务连接功能（例如[混合连接](app-service-hybrid-connections.md)）进行的所有通信均已加密。 
- 与 Azure PowerShell、Azure CLI、Azure SDK、REST API 等远程管理工具的连接均已加密。
- 24 小时威胁管理可保护基础结构和平台免受恶意软件、分布式拒绝服务 (DDoS)、中间人 (MITM) 和其他威胁的危害。

有关 Azure 中的基础结构和平台安全性的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/overview/trusted-cloud/)。

以下部分介绍如何进一步保护应用服务应用使其免受威胁。

## <a name="https-and-certificates"></a>HTTPS 和证书

应用服务允许通过 [HTTPS](https://wikipedia.org/wiki/HTTPS) 保护应用。 创建应用后，便可使用 HTTPS 访问其默认域名 (\<app_name>.azurewebsites.net)。 如果[为应用配置自定义域](app-service-web-tutorial-custom-domain.md)，则还应该[使用自定义证书对其进行保护](app-service-web-tutorial-custom-ssl.md)，以便客户端浏览器与自定义域建立安全的 HTTPS 连接。 可通过两种方式来执行此操作：

- **应用服务证书**：直接在 Azure 中创建证书。 该证书在 [Azure Key Vault](/azure/key-vault/) 中受到保护，并且可以导入到应用服务应用中。 有关详细信息，请参阅[为 Azure 应用服务购买和配置 SSL 证书](web-sites-purchase-ssl-web-site.md)。
- **第三方证书**：上传从可信证书颁发机构购买的自定义 SSL 证书，并将其绑定到应用服务应用。 应用服务既支持单域证书，也支持通配型证书。 它还支持用于测试目的的自签名证书。 有关详细信息，请参阅[将现有的自定义 SSL 证书绑定到 Azure Web 应用](app-service-web-tutorial-custom-ssl.md)。

## <a name="insecure-protocols-http-tls-10-ftp"></a>不安全的协议（HTTP、TLS 1.0、FTP）

为了保护应用免受所有未加密 (HTTP) 连接的攻击，应用服务提供一键式配置以实施 HTTPS。 不安全的请求在到达你的应用程序代码之前就会被拒绝。 有关详细信息，请参阅[实施 HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)。

[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) 等行业标准已不再将 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 视为安全协议。 应用服务允许通过[实施 TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112) 来禁用过时的协议。

应用服务支持使用 FTP 和 FTPS 来部署文件。 但是，如果可能的话，应使用 FTPS 而不是 FTP。 如果未使用这两种协议或其中一种协议，则应[将其禁用](app-service-deploy-ftp.md#enforce-ftps)。

## <a name="static-ip-restrictions"></a>静态 IP 限制

默认情况下，应用服务应用接受来自 Internet 的所有 IP 地址的请求，但你可以将该访问权限限定于一小部分 IP 地址。 通过 Windows 上的应用服务，可定义允许访问应用的 IP 地址的列表。 允许列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制](app-service-ip-restrictions.md)。

对于 Windows 上的应用服务，还可以通过配置 _web.config_ 来动态限制 IP 地址。有关详细信息，请参阅[动态 IP 安全性<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/)。

## <a name="client-authentication-and-authorization"></a>客户端身份验证和授权

Azure 应用服务提供用户或客户端应用的统包身份验证和授权。 启用后，它只需使用少量应用程序代码甚至不使用任何应用程序代码就可让用户和客户端应用登录。 你可以实施自己的身份验证和授权解决方案，或允许应用服务代为处理。 身份验证和授权模块在将 Web 请求交付给应用程序代码之前会对其进行处理，并在未经授权的请求到达代码之前便拒绝它们。

应用服务身份验证和授权支持多个验证提供程序，包括 Azure Active Directory、Microsoft 帐户、Facebook、Google 和 Twitter。 有关详细信息，请参阅 [Azure 应用服务中的身份验证和授权](app-service-authentication-overview.md)。

## <a name="service-to-service-authentication"></a>服务到服务身份验证

在向后端服务进行身份验证时，应用服务根据你的需要提供两种不同的机制：

- **服务标识**：使用应用本身的标识登录到远程资源。 通过应用服务可轻松创建[托管服务标识](app-service-managed-service-identity.md)，在向 [Azure SQL 数据库](/azure/sql-database/)或 [Azure Key Vault](/azure/key-vault/) 等其他服务进行身份验证时可使用该标识。 有关此方法的端到端教程，请参阅[使用托管服务标识确保从应用服务进行的 Azure SQL 数据库连接的安全](app-service-web-tutorial-connect-msi.md)。
- **代表 (OBO)**：代表用户对远程资源进行委托访问。 使用 Azure Active Directory 作为验证提供程序时，应用服务应用可以执行远程服务（例如 [Azure Active Directory 图形 API](../active-directory/develop/active-directory-graph-api.md) 或应用服务中的远程 API 应用）委托登录。 有关此方法的端到端教程，请参阅[在 Azure 应用服务中对用户进行端到端身份验证和授权](app-service-web-tutorial-auth-aad.md)。

## <a name="connectivity-to-remote-resources"></a>远程资源连接

应用可能需要访问三种类型的远程资源： 

- [Azure 资源](#azure-resources)
- [Azure 虚拟网络中的资源](#resources-inside-an-azure-virtual-network)
- [本地资源](#on-premises-resources)

在上述每种情况下，应用服务都提供了一种安全连接方式，但你仍应遵守安全最佳做法。 例如，即使后端资源允许未加密连接，也始终使用加密连接。 此外，请确保后端 Azure 服务允许使用最少的一组 IP 地址。 可以在 [Azure 应用服务的入站和出站 IP 地址](app-service-ip-addresses.md)中找到应用的出站 IP 地址。

### <a name="azure-resources"></a>Azure 资源

当应用连接到 Azure 资源（例如 [SQL 数据库](/services/sql-database/)和 [Azure 存储](/azure/storage/)）时，连接一直保持在 Azure 中，不会跨越任何网络边界。 但是，连接经过 Azure 中的共享网络，因此请务必确保连接已加密。 

如果应用托管在[应用服务环境](environment/intro.md)中，则应该[使用虚拟网络服务终结点连接到受支持的 Azure 服务](../virtual-network/virtual-network-service-endpoints-overview.md)。

### <a name="resources-inside-an-azure-virtual-network"></a>Azure 虚拟网络中的资源

应用可以通过[虚拟网络集成](web-sites-integrate-with-vnet.md)访问 [Azure 虚拟网络](/azure/virtual-network/)中的资源。 该集成通过点到站点 VPN 随虚拟网络一起建立。 之后，应用可以使用虚拟网络资源的专用 IP 地址来访问这些资源。 但是，点到站点连接仍然会遍历 Azure 中的共享网络。 

若要将资源连接与 Azure 中的共享网络完全隔离，请在[应用服务环境](environment/intro.md)中创建应用。 由于应用服务环境始终部署到专用虚拟网络，因此，应用与虚拟网络资源之间的连接是完全隔离的。 有关应用服务环境中网络安全的其他方面，请参阅[网络隔离](#network-isolation)。

### <a name="on-premises-resources"></a>本地资源

可通过三种方式安全地访问本地资源（如数据库）： 

- [混合连接](app-service-hybrid-connections.md)：通过 TCP 隧道建立到远程资源的点到点连接。 TCP 隧道是使用 TLS 1.2 通过共享访问签名 (SAS) 密钥建立的。
- 通过站点到站点 VPN 建立的[虚拟网络集成](web-sites-integrate-with-vnet.md)：如 [Azure 虚拟网络中的资源](#resources-inside-an-azure-virtual-network)中所述，但虚拟网络可通过[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接到本地网络。 在这种网络拓扑中，应用可以像连接到虚拟网络中的其他资源一样连接到本地资源。
- 带有站点到站点 VPN 的[应用服务环境](environment/intro.md)：如 [Azure 虚拟网络中的资源](#resources-inside-an-azure-virtual-network)中所述，但虚拟网络可通过[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接到本地网络。 在这种网络拓扑中，应用可以像连接到虚拟网络中的其他资源一样连接到本地资源。

## <a name="application-secrets"></a>应用程序密钥

请勿将应用程序密钥（例如数据库凭据、API 令牌和私钥）存储在代码或配置文件中。 广为接受的方法是使用所选语言的标准模式将它们作为[环境变量](https://wikipedia.org/wiki/Environment_variable)进行访问。 在应用服务中，环境变量通过[应用设置](web-sites-configure.md#app-settings)（对于 .NET 应用程序则为[连接字符串](web-sites-configure.md#connection-strings)）进行定义。 应用设置和连接字符串以加密方式存储在 Azure 中，只有在应用启动并将其注入应用的进程内存之前才会对其进行解密。 加密密钥会定期轮换。

或者，可以将应用服务应用与 [Azure Key Vault](/azure/key-vault/) 集成，以实现高级密钥管理。 通过[使用托管服务标识访问 Key Vault](../key-vault/tutorial-web-application-keyvault.md)，应用服务应用可以安全地访问所需的密钥。

## <a name="network-isolation"></a>网络隔离

除了**独立**定价层，所有层都在应用服务的共享网络基础结构上运行应用。 例如，公共 IP 地址和前端负载均衡器与其他租户共享。 通过在专用的[应用服务环境](environment/intro.md)中运行应用，**独立**层可提供完整的网络隔离。 应用服务环境在你自己的 [Azure 虚拟网络](/azure/virtual-network/)实例中运行。 它允许： 

- 使用[网络安全组](../virtual-network/virtual-networks-nsg.md)限制网络访问权限。 
- 通过专用的公共终结点为应用提供专用前端。
- 使用内部负载均衡器 (ILB) 为内部应用程序提供服务，该内部负载均衡器仅允许从 Azure 虚拟网络内部进行访问。 ILB 有一个来自专用子网的 IP 地址，它可以让应用与 Internet 完全隔离。
- [在 Web 应用程序防火墙 (WAF) 后面使用 ILB](environment/integrate-with-application-gateway.md)。 WAF 为面向公众的应用程序提供企业级保护，例如 DDoS 防护、URI 筛选和 SQL 注入防护。

有关详细信息，请参阅 [Azure 应用服务环境简介](environment/intro.md)。