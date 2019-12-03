---
title: 安全建议
description: 实施安全建议，以帮助满足我们的共享责任模型中所述的安全要求。 提高应用程序的安全性。
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684110"
---
# <a name="security-recommendations-for-app-service"></a>应用服务的安全建议

本文包含有关 Azure App Service 的安全建议。 实施这些建议可帮助你满足我们的共享责任模型中所述的安全要求，并将提高你的 Web 应用解决方案的整体安全性。 若要详细了解 Microsoft 如何实现服务提供商责任，请阅读[Azure 基础结构安全性](../security/fundamentals/infrastructure.md)。

## <a name="general"></a>一般信息

| 建议 | 注释 |
|-|-|----|
| 保持最新状态 | 使用支持的平台、编程语言、协议和框架的最新版本。 |

## <a name="identity-and-access-management"></a>身份和访问管理

| 建议 | 注释 |
|-|----|
| 禁用匿名访问 | 除非你需要支持匿名请求，否则请禁用匿名访问。 有关 Azure App Service 身份验证选项的详细信息，请参阅[Azure App Service 中的身份验证和授权](overview-authentication-authorization.md)。|
| 要求身份验证 | 请尽可能使用应用服务身份验证模块，而不是编写代码来处理身份验证和授权。 请参阅[Azure App Service 中的身份验证和授权](overview-authentication-authorization.md)。 |
| 使用经过身份验证的访问来保护后端资源 | 可以使用用户的标识，也可以使用应用程序标识向后端资源进行身份验证。 如果选择使用应用程序标识，请使用[托管标识](overview-managed-identity.md)。
| 需要客户端证书身份验证 | 客户端证书身份验证通过只允许来自可以使用您提供的证书进行身份验证的客户端的连接，从而提高安全性。 |

## <a name="data-protection"></a>数据保护

| 建议 | 注释 |
|-|-|
| 将 HTTP 重定向到 HTTPs | 默认情况下，客户端可以使用 HTTP 或 HTTPS 连接到 web 应用。 建议将 HTTP 重定向到 HTTPs，因为 HTTPS 使用 SSL/TLS 协议来提供安全连接，此连接同时进行加密和身份验证。 |
| 加密与 Azure 资源的通信 | 当应用连接到 Azure 资源（例如[SQL 数据库](https://azure.microsoft.com/services/sql-database/)或[azure 存储](/azure/storage/)）时，连接将保留在 azure 中。 由于连接通过 Azure 中的共享网络，因此应始终加密所有通信。 |
| 需要最新的 TLS 版本 | 由于2018新 Azure App Service 应用使用 TLS 1.2。 较新版本的 TLS 包括比早期协议版本更高的安全性。 |
| 使用 FTPS | 应用服务支持使用 FTP 和 FTPS 来部署文件。 如果可能，请使用 FTPS 而不是 FTP。 如果未使用这两种协议或其中一种协议，则应[将其禁用](deploy-ftp.md#enforce-ftps)。 |
| 保护应用程序数据 | 不要在代码或配置文件中存储应用程序机密，如数据库凭据、API 令牌或私钥。 广为接受的方法是使用所选语言的标准模式将它们作为[环境变量](https://wikipedia.org/wiki/Environment_variable)进行访问。 在 Azure App Service 中，可以通过[应用设置](web-sites-configure.md)和[连接字符串](web-sites-configure.md)来定义环境变量。 应用设置和连接字符串存储在 Azure 中。 应用程序设置仅在应用程序启动时被解密，然后才会被注入到应用程序内存中。 加密密钥会定期轮换。 或者，你可以将 Azure App Service 应用与用于高级机密管理的[Azure Key Vault](/azure/key-vault/)集成。 通过[使用托管标识访问 Key Vault](../key-vault/tutorial-web-application-keyvault.md)，应用服务应用可以安全地访问所需的机密。 |

## <a name="networking"></a>网络

| 建议 | 注释 |
|-|-|
| 使用静态 IP 限制 | Windows 上的 Azure App Service 允许你定义允许访问应用的 IP 地址的列表。 允许列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制](app-service-ip-restrictions.md)。  |
| 使用独立定价层 | 除隔离定价层以外，所有层都在 Azure App Service 中的共享网络基础结构上运行应用。 通过在专用[应用服务环境](environment/intro.md)中运行应用，隔离层可提供完整的网络隔离。 应用服务环境在你自己的 [Azure 虚拟网络](/azure/virtual-network/)实例中运行。|
| 访问本地资源时使用安全连接 | 你可以使用[混合连接](app-service-hybrid-connections.md)、[虚拟网络集成](web-sites-integrate-with-vnet.md)或[应用服务环境](environment/intro.md)来连接到本地资源。 |
| 限制入站网络流量的暴露 | 网络安全组允许你限制网络访问和控制公开的终结点的数量。 有关详细信息，请参阅[如何控制到应用服务环境的入站流量](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>监视

| 建议 | 注释 |
|-|-|
|使用 Azure 安全中心标准层 | [Azure 安全中心](../security-center/security-center-app-services.md)与 Azure App Service 本机集成。 它可以运行评估并提供安全建议。 |

## <a name="next-steps"></a>后续步骤

请与应用程序提供商联系，查看是否存在其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../security/fundamentals/abstract-develop-secure-apps.md)。
