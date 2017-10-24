---
title: "使用 Azure App Service 保护 PaaS Web 和移动应用程序 | Microsoft Docs"
description: " 了解有关保护 PaaS Web 和移动应用程序的 Azure App Service 安全最佳实践。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: terrylan
ms.openlocfilehash: f1f08133b4601c7260687a4b281b461dbf4d611e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>使用 Azure App Service 保护 PaaS Web 和移动应用程序

本文介绍有关保护 PaaS Web 和移动应用程序的 [Azure App Service](https://azure.microsoft.com/services/app-service/) 安全最佳实践。 这些最佳实践衍生自我们的 Azure 经验和客户经验。

## <a name="azure-app-service"></a>Azure 应用服务
[Azure App Service](../app-service/app-service-web-overview.md) 是一个 PaaS 产品，可创建适用于任何平台或设备的 Web 和移动应用，并可连接到云中或本地任何位置的数据。 应用服务所包括的 Web 功能和移动功能是以前作为 Azure 网站和 Azure 移动服务单独交付的。 它还包括各种新功能，可以实现业务流程的自动化，并可托管云 API。 应用服务以单个集成服务的形式为 Web、移动和集成方案提供一组丰富的功能。

## <a name="best-practices"></a>最佳实践

使用应用服务时，请遵循以下最佳实践：

- [通过 Azure Active Directory (AD) 进行身份验证](../app-service/app-service-authentication-overview.md)。 应用服务为标识提供者提供 OAuth 2.0 服务。 OAuth 2.0 注重简化客户端开发人员的工作，同时为 Web 应用程序、桌面应用程序和移动电话提供特定的授权流。 Azure AD 使用 OAuth 2.0，可让你授予移动和 Web 应用程序的访问权限。
- 根据“需要知道”和“最低权限”安全原则限制访问。 对于想要实施数据访问安全策略的组织，限制访问是必须要做的事。 基于角色的访问控制 (RBAC) 可用于向特定范围的用户、组和应用程序分配权限。 若要了解有关向用户授予应用程序访问权限的详细信息，请参阅[访问管理入门](../active-directory/role-based-access-control-what-is.md)。
- 保护密钥。 如果丢失了订阅密钥，安全做得再好也无济于事。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 通过密钥保管库，可以使用受硬件安全模块 (HSM) 保护的密钥，来加密密钥和机密（例如身份验证密钥、存储帐户密钥、数据加密密钥、.PFX 文件和密码）。 为了提升可靠性，可以在 HSM 中导入或生成密钥。 请参阅 [Azure Key Vault](../key-vault/key-vault-whatis.md) 了解详细信息。 还可以使用 Key Vault 和自动续订来管理 TLS 证书。
- 限制传入的源 IP 地址。 [应用服务环境](../app-service/environment/intro.md)提供虚拟网络集成功能，可帮助你通过网络安全组 (NSG) 限制传入的源 IP 地址。 如果不熟悉 Azure 虚拟网络 (VNET)，可使用此功能将多个 Azure 资源放置在可以控制其访问权限但无法通过 Internet 路由的网络中。 若要了解详细信息，请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。

## <a name="next-steps"></a>后续步骤
本文介绍了有关保护 PaaS Web 和移动应用程序的一系列应用服务安全最佳实践。 若要了解有关保护 PaaS 部署的详细信息，请参阅：

- [保护 PaaS 部署](security-paas-deployments.md)
- [使用 Azure SQL 数据库和 SQL 数据仓库保护 PaaS Web 和移动应用程序](security-paas-applications-using-sql.md)
