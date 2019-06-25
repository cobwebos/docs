---
title: 针对 Azure 应用服务的安全建议
description: Azure 应用服务的安全建议。 实施这些建议将帮助你满足安全要求，我们共担责任模型，将中所述提高 web 应用解决方案的总体安全性。
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.openlocfilehash: f50532a21d11d2f7142fa3ee7ed08f759df36d52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207536"
---
# <a name="security-recommendations-for-app-service"></a>针对应用服务的安全建议

本文包含 Azure 应用服务的安全建议。 实现这些建议将帮助你满足安全要求，我们共担责任模型，将中所述改善你的 Web 应用解决方案的总体安全性。 有关 Microsoft 的详细信息执行的操作以满足服务提供程序的责任，阅读[Azure 基础结构安全](../security/azure-security-infrastructure.md)。

## <a name="general"></a>常规

| 建议 | 注释 |
|-|-|----|
| 保持最新状态 | 使用最新版本的受支持的平台，编程语言、 协议和框架。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 |
|-|----|
| 禁用匿名访问 | 除非你需要支持匿名请求，禁用匿名访问。 Azure 应用服务身份验证选项的详细信息，请参阅[身份验证和 Azure 应用服务中的授权](overview-authentication-authorization.md)。|
| 要求进行身份验证 | 只要有可能，使用而不是编写代码的应用服务身份验证模块来处理身份验证和授权。 请参阅[身份验证和 Azure 应用服务中的授权](overview-authentication-authorization.md)。 |
| 保护与身份验证的访问的后端资源 | 可以使用用户的标识，也可以使用应用程序标识对后端资源进行身份验证。 当你选择使用应用程序标识使用[托管标识](overview-managed-identity.md)。
| 需要客户端证书身份验证 | 客户端证书身份验证通过仅允许来自客户端可以使用你提供的证书身份验证的连接可以提高安全性。 |

## <a name="data-protection"></a>数据保护

| 建议 | 注释 |
|-|-|
| 将 HTTP 到 HTTPs 重定向 | 默认情况下，客户端可以通过使用 HTTP 或 HTTPS 连接到 web 应用。 我们建议将 HTTP 重定向到 HTTPs，因为 HTTPS 使用 SSL/TLS 协议来提供安全连接，这既是加密和身份验证。 |
| 对 Azure 资源的通信进行加密 | 当您的应用程序连接到 Azure 资源，如[SQL 数据库](https://azure.microsoft.com/services/sql-database/)或[Azure 存储](/azure/storage/)，连接会一直保持在 Azure 中。 由于连接停止通过在 Azure 中的共享网络，应始终加密所有通信。 |
| 需要可能的最新的 TLS 版本 | 自 2018年后新的 Azure 应用服务应用程序使用 TLS 1.2。 较新版本的 TLS 协议版本较旧的基础上包括安全性改进。 |
| 使用 FTPS | 应用服务支持使用 FTP 和 FTPS 来部署文件。 使用 FTPS 而不是 FTP 在可能的情况。 如果未使用这两种协议或其中一种协议，则应[将其禁用](deploy-ftp.md#enforce-ftps)。 |
| 保护应用程序数据 | 不要将应用程序机密，如数据库凭据、 API 令牌或私钥存储在代码或配置文件。 广为接受的方法是使用所选语言的标准模式将它们作为[环境变量](https://wikipedia.org/wiki/Environment_variable)进行访问。 在 Azure 应用服务中，可以定义通过环境变量[应用设置](web-sites-configure.md)并[连接字符串](web-sites-configure.md)。 在 Azure 中加密存储应用设置和连接字符串。 仅在应用启动时被注入到应用程序的进程内存之前被解密的应用程序设置。 加密密钥会定期轮换。 或者，可以将使用在 Azure 应用服务应用程序集成[Azure 密钥保管库](/azure/key-vault/)高级的机密管理的。 通过[使用托管标识访问 Key Vault](../key-vault/tutorial-web-application-keyvault.md)，应用服务应用可以安全地访问所需的机密。 |

## <a name="networking"></a>网络

| 建议 | 注释 |
|-|-|
| 使用静态 IP 限制 | Windows 上的 azure 应用服务，可以定义允许访问您的应用程序的 IP 地址的列表。 允许列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制](app-service-ip-restrictions.md)。  |
| 使用独立定价层 | 除了独立定价层中，所有层在 Azure 应用服务中的共享的网络基础结构上都运行您的应用程序。 独立的层提供完整的网络隔离通过运行您的应用程序内的专用[应用服务环境](environment/intro.md)。 应用服务环境在你自己的 [Azure 虚拟网络](/azure/virtual-network/)实例中运行。|
| 使用安全连接时访问本地资源 | 可以使用[混合连接](app-service-hybrid-connections.md)，[虚拟网络集成](web-sites-integrate-with-vnet.md)，或[应用服务环境的](environment/intro.md)连接到本地资源。 |
| 降低遭受拒绝入站的网络流量 | 网络安全组允许您以限制网络访问和控制公开的终结点的数量。 有关详细信息，请参阅[如何为控制入站流量流向应用服务环境](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>监视

| 建议 | 注释 |
|-|-|
|使用 Azure 安全中心标准层 | [Azure 安全中心](../security-center/security-center-app-services.md)与 Azure 应用服务本机集成。 它可以运行评估，并提供安全建议。 |

## <a name="next-steps"></a>后续步骤

请与您的应用程序提供商，以查看是否有其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../security/abstract-develop-secure-apps.md)。
