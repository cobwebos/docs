---
title: 什么是 Azure 密钥保管库？ | Microsoft Docs
description: 了解 Azure Key Vault 如何保护云应用程序和服务使用的加密密钥和机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cf8101923f898c4ce190f3eaf2f3a9b2430cc723
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885123"
---
# <a name="what-is-azure-key-vault"></a>什么是 Azure 密钥保管库？

云应用程序和服务使用加密密钥和机密来帮助保护信息安全。 Azure Key Vault 保护这些密钥和机密。 使用 Key Vault 时，可以使用受硬件安全模块（Hsm）保护的密钥来加密身份验证密钥、存储帐户密钥、数据加密密钥、.pfx 文件和密码。

Key Vault 有助于解决以下问题：

- **机密管理**：安全地存储令牌、密码、证书、API 密钥和其他机密，并对其访问进行严格控制。
- **密钥管理**：创建和控制用于加密数据的加密密钥。 
- **证书管理**：预配、管理和部署公用和专用安全套接字层/传输层安全性 (SSL/TLS) 证书，这些证书可以与 Azure 以及你的内部连接资源配合使用。 
- **存储 hsm 支持的机密**：使用软件或 FIPS 140-2 第2级验证的 Hsm 来帮助保护机密和密钥。

## <a name="basic-concepts"></a>基本概念

Azure Key Vault 是一个用于安全地存储和访问机密的工具。 机密是你希望严格控制对其的访问的任何东西，例如 API 密钥、密码或证书。 保管库是机密的逻辑组。

下面是其他重要的术语：

- **租户**：租户是拥有和管理特定的 Microsoft 云服务实例的组织。 它通常用来引用组织的 Azure 和 Office 365 服务集。

- **保管库所有者**：保管库所有者可以创建密钥保管库并获得它的完全访问权限和控制权。 保管库所有者还可以设置审核来记录谁访问了机密和密钥。 管理员可以控制密钥生命周期。 他们可以滚动到密钥的新版本、对其进行备份，以及执行相关的任务。

- **保管库使用者**：当保管库所有者为保管库使用者授予了访问权限时，使用者可以对密钥保管库内的资产执行操作。 可用操作取决于所授予的权限。

- **资源**：资源是可通过 Azure 获取的可管理项。 常见示例包括虚拟机、存储帐户、Web 应用、数据库和虚拟网络。 这只是其中一小部分。

- **资源组**：资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

- **服务主体**：Azure 服务主体是用户创建的应用、服务和自动化工具用来访问特定 Azure 资源的安全标识。 可将其视为具有特定角色，并且权限受到严格控制的“用户标识”（用户名和密码，或者证书）。 与普通的用户标识不同，服务主体只需执行特定的操作。 如果只向它授予执行管理任务所需的最低权限级别，则可以提高安全性。

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)：Azure AD 是租户的 Active Directory 服务。 每个目录有一个或多个域。 每个目录可以有多个订阅与之关联，但只有一个租户。

- **Azure 租户 ID**：租户 ID 是用于在 Azure 订阅中标识 Azure AD 实例的唯一方法。

- **托管标识**：虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 使用托管标识可为 Azure 服务提供 Azure AD 中的自动托管标识，更巧妙地解决了这个问题。 可以使用此标识向支持 Azure AD 身份验证的密钥保管库或任何服务证明身份，而无需在代码中放入任何凭据。 有关详细信息，请参阅下图，并[概述 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

    ![Azure 资源的托管标识的工作原理示意图](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>身份验证
若要对 Key Vault 执行任何操作，首先需要对其进行身份验证。 有三种方法可以对 Key Vault 进行身份验证：

- [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)：当你在 Azure 中的虚拟机上部署应用时，可以为虚拟机分配可访问 Key Vault 的标识。 你还可以将标识分配给[其他 Azure 资源](../active-directory/managed-identities-azure-resources/overview.md)。 此方法的优点是应用或服务未管理第一个机密的旋转。 Azure 会自动轮换标识。 建议使用此方法作为最佳做法。 
- **服务主体和证书**：可以使用服务主体和有权访问 Key Vault 的关联证书。 不建议采用这种方法，因为应用程序所有者或开发人员必须轮换该证书。
- **服务主体和机密**：尽管可以使用服务主体和机密对 Key Vault 进行身份验证，但我们不建议这样做。 很难自动将用于身份验证的启动密钥轮换到 Key Vault。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表更好地了解密钥保管库如何帮助达到开发人员和安全管理员的需求。

| Role | 问题陈述 | Azure 密钥保管库已解决问题 |
| --- | --- | --- |
| Azure 应用程序开发人员 |"我想要编写使用密钥进行签名和加密的 Azure 应用程序。 但我希望这些密钥在应用程序外部，以便解决方案适用于地理位置分散的应用程序。 <br/><br/>希望这些密钥和机密都是经过加密的，而无需自己编写代码。 除此之外，希望这些密钥和机密在应用程序中易于使用，能实现最佳性能。” |√ 密钥存储在保管库中，可按需由 URI 调用。<br/><br/> √ 密钥由 Azure 使用行业标准的算法、密钥长度和硬件安全模块进行保护。<br/><br/> √ 密钥会在与应用程序处于同一 Azure 数据中心的 HSM 中进行处理。 与驻留在单独位置（如本地）的密钥相比，此方法提供了更好的可靠性和更低的延迟。 |
| 软件即服务 (SaaS) 开发人员 |“对于客户的租户密钥和机密，我不想承担任何实际或潜在法律责任。 <br/><br/>我希望客户拥有并管理他们自己的密钥，使我能够将全部精力集中在我的专长上，也就是提供核心软件功能。” |√ 客户可以将他们自己的密钥导入 Azure 并进行管理。 当 SaaS 应用程序需要使用客户的密钥来执行加密操作时，Key Vault 将代表应用程序执行这些操作。 应用程序将不看到客户的密钥。 |
| 首席安全官 (CSO) |“我想要知道应用程序是否遵循 FIPS 140-2 Level 2 HSM 的安全密钥管理规范。 <br/><br/>我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。 <br/><br/>此外，即使我们使用多个 Azure 服务和资源，我也想要从 Azure 中的单个位置管理密钥。” |√ HSM 通过了 FIPS 140-2 第 2 级验证。<br/><br/>√ 密钥保管库设计用于确保 Microsoft 不会看到或提取密钥。<br/><br/>√ 以近实时方式记录密钥的使用。<br/><br/>√ 无论 Azure 中拥有的密钥数量，以及支持的地区和使用这些密钥的应用程序，保管库都仅提供单个界面。 |

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管 Key Vault 能够为开发人员和安全管理员提供便利，但管理其他 Azure 服务的管理员也可以实现和管理 Key Vault。 例如，此管理员可以使用 Azure 订阅登录、创建组织用来存储密钥的保管库，并负责执行操作任务，如下所示：

- 创建或导入密钥或机密
- 吊销或删除密钥或机密
- 授权用户或应用程序访问密钥保管库，使它们能够管理或使用其密钥和机密
- 配置密钥用法（例如，签名或加密）
- 监视密钥用法

该管理员然后会为开发人员提供 URI，方便其从应用程序进行调用。 该管理员也会将密钥使用日志记录信息提供给安全管理员。 

![Azure 密钥保管库的工作原理概述][1]

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅 [密钥保管库开发人员指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>后续步骤

了解如何[保护保管库](key-vault-secure-your-key-vault.md)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。
