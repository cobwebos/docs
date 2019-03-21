---
title: 什么是 Azure 密钥保管库？ | Microsoft Docs
description: 了解 Azure Key Vault 保护加密密钥和机密的云应用程序和服务使用的方式。
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 48ac0c3efe74723099e87a77871aa1a78834efbd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958525"
---
# <a name="what-is-azure-key-vault"></a>什么是 Azure 密钥保管库？

云应用程序和服务使用加密密钥和机密，以帮助确保信息安全。 Azure Key Vault 保护这些密钥和机密。 当您使用密钥保管库时，您可以使用硬件安全模块 (Hsm) 保护的密钥来加密身份验证密钥、 存储帐户密钥、 数据加密密钥、.pfx 文件和密码。

Key Vault 可帮助解决以下问题：

- **机密管理**:安全地存储和严密控制访问的令牌、 密码、 证书、 API 密钥和其他机密。
- **密钥管理**:创建和控制对数据进行加密的加密密钥。 
- **证书管理**:预配、 管理和部署适用于 Azure 和内部连接的资源的公钥和私钥安全套接字层/传输层安全性 (SSL/TLS) 证书。 
- **存储由 Hsm 支持的机密**:使用软件或 FIPS 140-2 级别 2 验证的 Hsm 来保护机密和密钥。

## <a name="basic-concepts"></a>基本概念

Azure Key Vault 是一个用于安全地存储和访问机密的工具。 机密是你希望严格控制对其的访问的任何东西，例如 API 密钥、密码或证书。 保管库是机密的逻辑组。

下面是其他重要的术语：

- **租户**：租户是拥有和管理特定的 Microsoft 云服务实例的组织。 通常用于指代的组织的 Azure 和 Office 365 服务集。

- **保管库所有者**：保管库所有者可以创建密钥保管库并获得它的完全访问权限和控制权。 保管库所有者还可以设置审核来记录谁访问了机密和密钥。 管理员可以控制密钥生命周期。 他们可以滚动到密钥的新版本、对其进行备份，以及执行相关的任务。

- **保管库使用者**：当保管库所有者为保管库使用者授予了访问权限时，使用者可以对密钥保管库内的资产执行操作。 可用操作取决于所授予的权限。

- **资源**：资源是可通过 Azure 获取的可管理项。 常见示例是虚拟机、 存储帐户、 web 应用、 数据库和虚拟网络。 有更多。

- **资源组**：资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

- **服务主体**：Azure 服务主体是用户创建的应用、 服务和自动化工具用于访问特定的 Azure 资源的安全标识。 将其视为"用户标识"(用户名和密码或证书) 与特定角色和权限受到严格控制。 与普通的用户标识不同，服务主体只需执行特定的操作。 如果您向它授予它需要对其执行管理任务的最小权限级别，它可以提高安全性。

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)：Azure AD 是租户的 Active Directory 服务。 每个目录有一个或多个域。 每个目录可以有多个订阅与之关联，但只有一个租户。

- **Azure 租户 ID**：租户 ID 是用于在 Azure 订阅中标识 Azure AD 实例的唯一方法。

- **托管标识**:虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 使用托管标识可为 Azure 服务提供 Azure AD 中的自动托管标识，更巧妙地解决了这个问题。 可以使用此标识向支持 Azure AD 身份验证的密钥保管库或任何服务证明身份，而无需在代码中放入任何凭据。 有关详细信息，请参阅下图中，[的 Azure 资源的管理的标识概述](../active-directory/managed-identities-azure-resources/overview.md)。

    ![如何管理 Azure 资源工作的标识的关系图](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
为使用密钥保管库的任何操作，首先需要对其进行身份验证。 有三种方法对密钥保管库进行身份验证：

- [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)：在部署 Azure 中的虚拟机上的应用程序时，可以将标识分配到你有权访问密钥保管库的虚拟机。 你还可以分配的身份[其他 Azure 资源](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 此方法的好处是应用程序或服务不管理的第一个机密轮换。 Azure 会自动轮换标识。 作为最佳做法，我们建议此方法。 
- **服务主体和证书**:可以使用服务主体和证书关联，有权访问密钥保管库。 我们不建议此方法，因为应用程序所有者或开发人员必须将该证书。
- **服务主体和机密**:尽管可以使用服务主体和机密对密钥保管库进行身份验证，但我们不建议这样。 很难自动轮换 bootstrap 的机密，用于向密钥保管库进行身份验证。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表更好地了解密钥保管库如何帮助达到开发人员和安全管理员的需求。

| 角色 | 问题陈述 | Azure 密钥保管库已解决问题 |
| --- | --- | --- |
| Azure 应用程序开发人员 |"我想要编写使用密钥进行签名和加密的 azure 应用程序。 但我希望这些密钥必须与我的应用程序分开，使解决方案适用于地理分散的应用程序。 <br/><br/>希望这些密钥和机密都是经过加密的，而无需自己编写代码。 除此之外，希望这些密钥和机密在应用程序中易于使用，能实现最佳性能。” |√ 密钥存储在保管库中，可按需由 URI 调用。<br/><br/> √ 密钥由 Azure 使用行业标准的算法、密钥长度和硬件安全模块进行保护。<br/><br/> √ 密钥会在与应用程序处于同一 Azure 数据中心的 HSM 中进行处理。 与驻留在单独位置（如本地）的密钥相比，此方法提供了更好的可靠性和更低的延迟。 |
| 软件即服务 (SaaS) 开发人员 |“对于客户的租户密钥和机密，我不想承担任何实际或潜在法律责任。 <br/><br/>我希望客户拥有并管理他们自己的密钥，使我能够将全部精力集中在我的专长上，也就是提供核心软件功能。” |√ 客户可以将他们自己的密钥导入 Azure 并进行管理。 当 SaaS 应用程序需要使用客户的密钥执行加密操作时，密钥保管库执行这些操作代表应用程序。 应用程序将不看到客户的密钥。 |
| 首席安全官 (CSO) |“我想要知道应用程序是否遵循 FIPS 140-2 Level 2 HSM 的安全密钥管理规范。 <br/><br/>我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。 <br/><br/>此外，即使我们使用多个 Azure 服务和资源，我也想要从 Azure 中的单个位置管理密钥。” |√ HSM 通过了 FIPS 140-2 第 2 级验证。<br/><br/>√ 密钥保管库设计用于确保 Microsoft 不会看到或提取密钥。<br/><br/>√ 以近实时方式记录密钥的使用。<br/><br/>√ 无论 Azure 中拥有的密钥数量，以及支持的地区和使用这些密钥的应用程序，保管库都仅提供单个界面。 |

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管密钥保管库开发人员和安全管理员提供便利，但它可以实现和管理组织的管理员负责管理其他 Azure 服务。 例如，此管理员可以使用 Azure 订阅，登录创建组织用来存储密钥，并负责执行类似这样的操作任务的保管库：

- 创建或导入密钥或机密
- 吊销或删除密钥或机密
- 授权用户或应用程序访问密钥保管库，使它们能够管理或使用其密钥和机密
- 配置密钥用法（例如，签名或加密）
- 监视密钥用法

此管理员然后开发人员从其应用程序调用的 Uri。 此管理员还可以安全管理员密钥用法日志记录信息。 

![Azure 密钥保管库的工作原理概述][1]

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅 [密钥保管库开发人员指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>后续步骤

了解如何[保护保管库](key-vault-secure-your-key-vault.md)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。
