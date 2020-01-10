---
title: 什么是 Azure Key Vault？ | Microsoft Docs
description: 了解 Azure Key Vault 如何保护云应用程序和服务使用的加密密钥和机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 7f32664f4bb9407d915b7909f9ecdeebe96bd551
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771932"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault 基本概念

Azure Key Vault 是一个用于安全地存储和访问机密的工具。 机密是你希望严格控制对其的访问的任何东西，例如 API 密钥、密码或证书。 保管库是机密的逻辑组。

下面是其他重要术语：

- **租户**：租户是拥有和管理特定的 Microsoft 云服务实例的组织。 它最常用于指代组织的一组 Azure 和 Office 365 服务。

- **保管库所有者**：保管库所有者可以创建密钥保管库并获得它的完全访问权限和控制权。 保管库所有者还可以设置审核来记录谁访问了机密和密钥。 管理员可以控制密钥生命周期。 他们可以滚动到密钥的新版本、对其进行备份，以及执行相关的任务。

- **保管库使用者**：当保管库所有者为保管库使用者授予了访问权限时，使用者可以对密钥保管库内的资产执行操作。 可用操作取决于所授予的权限。

- **资源**：资源是可通过 Azure 获取的可管理项。 常见示例包括虚拟机、存储帐户、web 应用、数据库和虚拟网络。 还有很多。

- **资源组**：资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

- **服务主体**： Azure 服务主体是用户创建的应用、服务和自动化工具用于访问特定 Azure 资源的安全标识。 将其视为具有特定角色的 "用户标识" （用户名和密码或证书），并严格控制权限。 与普通的用户标识不同，服务主体只需执行特定的操作。 如果仅授予执行管理任务所需的最低权限级别，则它会提高安全性。

- [Azure Active Directory （Azure AD）](../active-directory/active-directory-whatis.md)： Azure AD 是租户的 Active Directory 服务。 每个目录有一个或多个域。 每个目录可以有多个订阅与之关联，但只有一个租户。

- **Azure 租户 ID**：租户 ID 是用于在 Azure 订阅中标识 Azure AD 实例的唯一方法。

- **托管标识**： Azure Key Vault 提供了一种安全地存储凭据和其他密钥和机密的方式，但你的代码需要进行身份验证才能 Key Vault 检索它们。 使用托管标识可为 Azure 服务提供 Azure AD 中的自动托管标识，更巧妙地解决了这个问题。 可以使用此标识向支持 Azure AD 身份验证的密钥保管库或任何服务证明身份，而无需在代码中放入任何凭据。 有关详细信息，请参阅下图，并[概述 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

    ![Azure 资源的托管标识的工作原理示意图](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>身份验证
若要对 Key Vault 执行任何操作，首先需要对其进行身份验证。 有三种方法可以对 Key Vault 进行身份验证：

- [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)：在 azure 中的虚拟机上部署应用时，可以将标识分配给可访问 Key Vault 的虚拟机。 你还可以将标识分配给[其他 Azure 资源](../active-directory/managed-identities-azure-resources/overview.md)。 此方法的优点是应用或服务未管理第一个机密的旋转。 Azure 会自动轮换标识。 建议使用此方法作为最佳做法。 
- **服务主体和证书**：你可以使用服务主体和有权访问 Key Vault 的关联证书。 不建议采用这种方法，因为应用程序所有者或开发人员必须轮换该证书。
- **服务主体和机密**：尽管你可以使用服务主体和机密对 Key Vault 进行身份验证，但我们不建议这样做。 很难自动将用于身份验证的启动密钥轮换到 Key Vault。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表更好地了解密钥保管库如何帮助达到开发人员和安全管理员的需求。

| 角色 | 问题陈述 | Azure 密钥保管库已解决问题 |
| --- | --- | --- |
| Azure 应用程序开发人员 |"我想要编写使用密钥进行签名和加密的 Azure 应用程序。 但我希望这些密钥在应用程序外部，以便解决方案适用于地理位置分散的应用程序。 <br/><br/>希望这些密钥和机密都是经过加密的，而无需自己编写代码。 除此之外，希望这些密钥和机密在应用程序中易于使用，能实现最佳性能。” |√ 密钥存储在保管库中，可按需由 URI 调用。<br/><br/> √ 密钥由 Azure 使用行业标准的算法、密钥长度和硬件安全模块进行保护。<br/><br/> √ 密钥会在与应用程序处于同一 Azure 数据中心的 HSM 中进行处理。 与驻留在单独位置（如本地）的密钥相比，此方法提供了更好的可靠性和更低的延迟。 |
| 软件即服务 (SaaS) 开发人员 |“对于客户的租户密钥和机密，我不想承担任何实际或潜在法律责任。 <br/><br/>我希望客户拥有并管理他们自己的密钥，使我能够将全部精力集中在我的专长上，也就是提供核心软件功能。” |√ 客户可以将他们自己的密钥导入 Azure 并进行管理。 当 SaaS 应用程序需要使用客户的密钥来执行加密操作时，Key Vault 会代表应用程序执行这些操作。 应用程序将不看到客户的密钥。 |
| 首席安全官 (CSO) |“我想要知道应用程序是否遵循 FIPS 140-2 Level 2 HSM 的安全密钥管理规范。 <br/><br/>我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。 <br/><br/>此外，即使我们使用多个 Azure 服务和资源，我也想要从 Azure 中的单个位置管理密钥。” |√ HSM 通过了 FIPS 140-2 第 2 级验证。<br/><br/>√ 密钥保管库设计用于确保 Microsoft 不会看到或提取密钥。<br/><br/>√ 以近实时方式记录密钥的使用。<br/><br/>√ 无论 Azure 中拥有的密钥数量，以及支持的地区和使用这些密钥的应用程序，保管库都仅提供单个界面。 |

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管 Key Vault 可为开发人员和安全管理员提供便利，但它可由管理其他 Azure 服务的组织管理员实施和管理。 例如，此管理员可以使用 Azure 订阅登录，为要在其中存储密钥的组织创建保管库，并负责执行如下操作任务：

- 创建或导入密钥或机密
- 吊销或删除密钥或机密
- 授权用户或应用程序访问密钥保管库，使它们能够管理或使用其密钥和机密
- 配置密钥用法（例如，签名或加密）
- 监视密钥用法

然后，此管理员将为开发人员提供从其应用程序调用的 Uri。 此管理员还向安全管理员提供密钥使用情况日志记录信息。 

![Azure 密钥保管库的工作原理概述][1]

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅 [密钥保管库开发人员指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>后续步骤

了解如何[保护你的保管库](key-vault-secure-your-key-vault.md)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。
