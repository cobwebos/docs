---
title: 什么是 Azure 密钥保管库？ | Microsoft Docs
description: 了解 Azure Key Vault 如何保护云应用程序和服务使用的加密密钥和机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: dfb1ca4fc8f550c8ed6955adaca9082f0b6b79e6
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378995"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault 基本概念

Azure Key Vault 是一个用于安全地存储和访问机密的工具。 机密是你希望严格控制对其的访问的任何东西，例如 API 密钥、密码或证书。 保管库是机密的逻辑组。

下面是其他重要的术语：

- **租户**：租户是拥有和管理特定的 Microsoft 云服务实例的组织。 它通常用来引用组织的 Azure 和 Office 365 服务集。

- **保管库所有者**：保管库所有者可以创建密钥保管库并获得它的完全访问权限和控制权。 保管库所有者还可以设置审核来记录谁访问了机密和密钥。 管理员可以控制密钥生命周期。 他们可以滚动到密钥的新版本、对其进行备份，以及执行相关的任务。

- **保管库使用者**：当保管库所有者为保管库使用者授予了访问权限时，使用者可以对密钥保管库内的资产执行操作。 可用操作取决于所授予的权限。

- **资源**：资源是可通过 Azure 获取的可管理项。 常见示例包括虚拟机、存储帐户、Web 应用、数据库和虚拟网络。 这只是其中一小部分。

- **资源组**：资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

- **安全主体**： Azure 安全主体是用户创建的应用、服务和自动化工具用于访问特定 Azure 资源的安全标识。 可将其视为具有特定角色，并且权限受到严格控制的“用户标识”（用户名和密码，或者证书）。 与一般用户标识不同，安全主体只需执行特定的操作。 如果只向它授予执行管理任务所需的最低权限级别，则可以提高安全性。 与应用程序或服务一起使用的安全主体专门称为 **服务主体**。

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md)：Azure AD 是租户的 Active Directory 服务。 每个目录有一个或多个域。 每个目录可以有多个订阅与之关联，但只有一个租户。

- **Azure 租户 ID**：租户 ID 是用于在 Azure 订阅中标识 Azure AD 实例的唯一方法。

- **托管标识**：虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 使用托管标识可为 Azure 服务提供 Azure AD 中的自动托管标识，更巧妙地解决了这个问题。 可以使用此标识向支持 Azure AD 身份验证的密钥保管库或任何服务证明身份，而无需在代码中放入任何凭据。 有关详细信息，请参阅下图以及 [Azure 资源的托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

    ![Azure 资源的托管标识的工作原理图](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>身份验证
若要使用密钥保管库执行任何操作，首先需要向其进行身份验证。 可通过三种方式向密钥保管库进行身份验证：

- [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)：在 Azure 中的虚拟机上部署应用时，可以为虚拟机分配具有密钥保管库访问权限的标识。 还可以将标识分配给[其他 Azure 资源](../../active-directory/managed-identities-azure-resources/overview.md)。 这种方法的好处在于应用或服务不管理第一个机密的轮换。 Azure 会自动轮换标识。 我们将此方法作为最佳做法推荐。 
- **服务主体和证书**：可以使用服务主体和具有密钥保管库访问权限的关联证书。 我们不建议使用此方法，因为应用程序所有者或开发人员必须轮换证书。
- **服务主体和机密**：尽管可以使用服务主体和机密向密钥保管库进行身份验证，但我们不建议你这样做。 自动轮换用于向密钥保管库进行身份验证的启动机密非常困难。


## <a name="key-vault-roles"></a>Key Vault 角色

使用下表详细了解密钥保管库如何帮助达到开发人员和安全管理员的需求。

| 角色 | 问题陈述 | Azure 密钥保管库已解决问题 |
| --- | --- | --- |
| Azure 应用程序开发人员 |“我想要编写使用密钥进行签名和加密的 Azure 应用程序。 但我希望这些密钥与应用程序分开，使解决方案适用于在地理上分散的应用程序。 <br/><br/>希望这些密钥和机密都是经过加密的，而无需自己编写代码。 我还希望这些密钥和机密对于我来说很容易在应用程序中使用，并发挥最佳性能。” |√ 密钥存储在保管库中，可按需由 URI 调用。<br/><br/> √ 密钥由 Azure 使用行业标准的算法、密钥长度和硬件安全模块进行保护。<br/><br/> √ 密钥会在与应用程序处于同一 Azure 数据中心的 HSM 中进行处理。 与驻留在单独位置（如本地）的密钥相比，此方法提供了更好的可靠性和更低的延迟。 |
| 软件即服务 (SaaS) 开发人员 |“对于客户的租户密钥和机密，我不想承担任何实际或潜在法律责任。 <br/><br/>我希望客户拥有并管理其密钥，这样我就可以集中精力做我最擅长的事情，即提供核心软件功能。” |√ 客户可以将他们自己的密钥导入 Azure 并进行管理。 当 SaaS 应用程序需要使用客户的密钥来执行加密操作时，Key Vault 将代表应用程序执行这些操作。 应用程序看不到客户的密钥。 |
| 首席安全官 (CSO) |“我想要知道应用程序是否遵循 FIPS 140-2 Level 2 HSM 的安全密钥管理规范。 <br/><br/>我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。 <br/><br/>而且，尽管我们使用多个 Azure 服务和资源，但我想从 Azure 中的单个位置管理密钥。” |√ HSM 通过了 FIPS 140-2 第 2 级验证。<br/><br/>√ Key Vault 设计用于确保 Microsoft 不会看到或提取你的密钥。<br/><br/>√ 以近实时方式记录密钥的使用。<br/><br/>√ 无论 Azure 中拥有的密钥数量，以及支持的地区和使用这些密钥的应用程序，保管库都仅提供单个界面。 |

具有 Azure 订阅的任何人都可以创建和使用密钥保管库。 尽管 Key Vault 使开发人员和安全管理员受益，但是可以由管理其他 Azure 服务的组织管理员来实施和管理。 例如，此管理员可以使用 Azure 订阅登录、创建组织用来存储密钥的保管库，并负责执行操作任务，如下所示：

- 创建或导入密钥或机密
- 吊销或删除密钥或机密
- 授权用户或应用程序访问密钥保管库，使它们能够管理或使用其密钥和机密
- 配置密钥用法（例如，签名或加密）
- 监视密钥用法

该管理员然后会为开发人员提供 URI，方便其从应用程序进行调用。 该管理员也会将密钥使用日志记录信息提供给安全管理员。 

![Azure 密钥保管库的工作原理概述][1]

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅 [ 开发人员指南](developers-guide.md)。

## <a name="next-steps"></a>后续步骤

了解如何[保护保管库](secure-your-key-vault.md)。

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
大多数区域都提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。
