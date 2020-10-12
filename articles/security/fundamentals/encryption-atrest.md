---
title: Microsoft Azure 静态数据加密 | Microsoft Docs
description: 本文概述了 Microsoft Azure 静态数据加密及其整体功能和一般注意事项。
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: ec81a8f7f9d9f45f1d068a415a599ce30a0d4581
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397243"
---
# <a name="azure-data-encryption-at-rest"></a>Azure 静态数据加密

Microsoft Azure 提供了许多工具，可以使用它们根据你公司的安全性和合规性需求来保护数据。 本白皮书重点介绍：

- 如何在 Microsoft Azure 上对数据进行静态保护
- 讨论参与数据保护实现的各个组件
- 查看不同密钥管理保护方法的优点和缺点。

静态加密是常见的安全要求。 在 Azure 中，组织可以加密静态数据，而不会造成自定义密钥管理解决方案的风险或成本。 组织可以选择让 Azure 来全权管理静态加密。 另外，组织还可以通过各种选择来严格管理加密或加密密钥。

## <a name="what-is-encryption-at-rest"></a>什么是静态加密？

静态加密是指在持久保存数据时对数据进行编码（加密）。 Azure 中的静态加密设计使用对称加密根据简单的概念模型来快速加密和解密大量数据：

- 将使用对称加密密钥在将数据写入到存储时对数据进行加密。
- 当数据在内存中就绪可供使用时，将会使用同一加密密钥来解密该数据。
- 可以将数据分区，并可对每个分区使用不同的密钥。
- 必须将密钥存储在实施了基于标识的访问控制和审核策略的安全位置。 数据加密密钥通常由 Azure Key Vault 中的密钥加密密钥进行加密，以进一步限制访问。

在实践中，密钥管理和控制方案以及规模和可用性保证都需要其他构造。 下面描述的是 Microsoft Azure 静态加密概念和组件。

## <a name="the-purpose-of-encryption-at-rest"></a>静态加密的目的

静态加密为已存储的数据（静止的）提供数据保护。 对静态数据进行的攻击包括：试图获得存储数据的硬件的物理访问机会，然后盗用其中包含的数据。 发生此类攻击可能是由于服务器的硬盘驱动器在维护过程中处理不当，导致攻击者有机会拆除硬盘驱动器。 攻击者随后会将该硬盘驱动器置于受其控制的计算机中，尝试访问相关数据。

静态加密旨在防止攻击者访问未加密的数据，其方法是确保这些数据在磁盘上时是加密的。 如果攻击者获取了包含加密数据的硬盘驱动器但未获取加密密钥，则攻击者必须破解加密才能读取数据。 这种攻击比访问硬盘驱动器上的未加密数据要复杂得多，且消耗的资源也多得多。 因此，强烈建议使用静态加密。对于许多组织来说，这是需要完成的高优先级事项。

当组织需要进行数据治理并确保符合性时，可能也需要使用静态加密。 行业和政府法规（例如 HIPAA、PCI 和 FedRAMP）就数据保护和加密要求制定了具体的保障措施。 要符合这其中的许多法规，静态加密是一种必需的强制措施。 有关 Microsoft 的 FIPS 140-2 验证方法的详细信息，请参阅[美国联邦信息处理标准 (FIPS) 出版物 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2)。

除了满足合规要求以外，静态加密还能提供深层防御保护。 Microsoft Azure 为服务、应用程序和数据提供合规的平台。 此外，它还提供综合性的设施和物理安全性、数据访问控制和审核。 但是，必须提供额外的“重叠性”安全措施，以免出现其他某个安全措施失效的情况，而静态加密正好提供这样一道安全措施。

Microsoft 致力于提供跨云服务的静态加密选项，可让客户控制加密密钥和密钥使用日志。 另外，Microsoft 正在努力实现默认加密所有客户静态数据。

## <a name="azure-encryption-at-rest-components"></a>Azure 静态加密组件

如前所述，静态加密的目标是使用机密加密密钥来加密持久保存在磁盘上的数据。 若要实现该目标，必须为加密密钥提供安全的密钥创建、存储、访问控制和管理措施。 可以使用下图中介绍的术语来描述 Azure 服务静态加密实现，虽然细节可能有所不同。

![组件](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

对于静态加密模型来说，最重要的是加密密钥的存储位置以及对这些密钥的访问控制。 密钥需要严格的保护，但同时又要能够由指定的用户进行管理，并可供特定的服务使用。 对于 Azure 服务，建议使用 Azure Key Vault 作为密钥存储解决方案，它可以跨服务提供通常的管理体验。 密钥在密钥保管库中存储和管理，对密钥保管库的访问权限可以提供给用户或服务。 Azure Key Vault 支持客户创建密钥，也支持将导入的客户密钥用于客户管理的加密密钥方案。

### <a name="azure-active-directory"></a>Azure Active Directory

可以为 Azure Active Directory 帐户提供存储在 Azure Key Vault 中的密钥的使用权限，以便通过管理或访问这些密钥来完成静态加密的加密和解密操作。

### <a name="key-hierarchy"></a>密钥层次结构

在实施静态加密时，使用多个加密密钥。 将加密密钥存储在 Azure Key Vault 中可确保安全的密钥访问并可集中管理密钥。 但是，就批量加密和解密来说，通过服务在本地访问加密密钥比每项数据操作都要与 Key Vault 交互更为高效，可以提高加密强度和性能。 限制单个加密密钥的使用降低了密钥被盗用的风险，也降低了必须更换密钥时的重新加密成本。 Azure 静态加密模块使用一个密钥层次结构来解决所有这些需求，该密钥层次结构由以下类型的密钥构成：

- ** (DEK) 的数据加密密钥 ** –用于对分区或数据块进行加密的对称 AES256 密钥。  单个资源可能有多个分区和多个数据加密密钥。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，仅其关联的块中的数据需要使用新密钥重新加密。
- **密钥加密密钥 (KEK) ** –用于对数据加密密钥进行加密的加密密钥。 使用从不离开 Key Vault 的密钥加密密钥可以加密和控制数据加密密钥本身。 具有 KEK 访问权限的实体可能不同于需要 DEK 的实体。 实体可能会代理对 DEK 的访问以将每个 DEK 的访问限制到特定分区。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

使用密钥加密密钥加密的数据加密密钥将单独进行存储，只有能够访问密钥加密密钥的实体才能解密这些数据加密密钥。 支持各种不同的密钥存储模型。 有关详细信息，请参阅 [数据加密模型](encryption-models.md) 。

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Microsoft 云服务中的静态加密

Microsoft 云服务用于下述所有三个云模型：IaaS、PaaS、SaaS。 下面是在每个模型上使用该服务的示例：

- 软件服务，称为软件作为服务器或 SaaS，其中包含云提供的应用程序，如 Microsoft 365。
- 平台服务，方便客户在其应用程序中利用云，将云用于存储、分析和服务总线功能等。
- 基础结构服务，也称基础结构即服务 (IaaS)，方便客户部署托管在云中的操作系统和应用程序，并尽可能利用其他云服务。

### <a name="encryption-at-rest-for-saas-customers"></a>适合 SaaS 客户的静态加密

软件即服务 (SaaS) 客户通常会在每个服务中启用或提供静态加密。 Microsoft 365 有多个选项可供客户验证或启用静态加密。 有关 Microsoft 365 服务的信息，请参阅 [Microsoft 365 中的加密](/microsoft-365/compliance/encryption)。

### <a name="encryption-at-rest-for-paas-customers"></a>适合 PaaS 客户的静态加密

平台即服务 (PaaS) 客户的数据通常驻留在存储服务（例如 Blob 存储）中，但也可以缓存或存储在应用程序执行环境（例如虚拟机）中。 若要查看适用的静态加密选项，请检查下表中是否存在所用的存储和应用程序平台。

### <a name="encryption-at-rest-for-iaas-customers"></a>适合 IaaS 客户的静态加密

基础结构即服务 (IaaS) 客户可以使用各种服务和应用程序。 IaaS 服务可以在其 Azure 托管的虚拟机和 VHD 中通过 Azure 磁盘加密来启用静态加密。

#### <a name="encrypted-storage"></a>加密的存储

与 PaaS 一样，IaaS 解决方案可以利用其他存储静态加密数据的 Azure 服务。 在此类情况下，可以启用每个所用 Azure 服务提供的静态加密支持。 下表枚举了主要的存储、服务和应用程序平台以及所支持的静态加密模型。

#### <a name="encrypted-compute"></a>加密的计算

所有托管磁盘、快照和映像都通过服务管理的密钥使用存储服务加密进行加密。 更完整的静态加密解决方案可确保数据从不以未加密形式持久保存。 在虚拟机上处理数据时，可以将数据持久保存到 Windows 页面文件或 Linux 交换文件、故障转储或应用程序日志。 为了确保对该数据进行静态加密，IaaS 应用程序可以在 Azure IaaS 虚拟机（Windows 或 Linux）和虚拟磁盘上使用 Azure 磁盘加密。

#### <a name="custom-encryption-at-rest"></a>自定义静态加密

建议让 IaaS 应用程序尽可能利用 Azure 磁盘加密以及任何所用 Azure 服务提供的静态加密选项。 在某些情况下（例如加密要求异乎寻常，或者存储不是基于 Azure 的），IaaS 应用程序开发人员可能需要自行实施静态加密。 IaaS 解决方案开发人员可以利用某些 Azure 组件，改进与 Azure 管理的集成并更好地满足客户期望。 具体说来，开发人员应该使用 Azure Key Vault 服务为其客户提供安全的密钥存储，以及提供与大多数 Azure 平台服务一致的密钥管理选项。 另外，自定义解决方案应通过 Azure 托管服务标识来允许服务帐户访问加密密钥。 有关 Azure Key Vault 和托管服务标识的开发人员信息，请参阅各自的 SDK。

## <a name="azure-resource-providers-encryption-model-support"></a>Azure 资源提供程序加密模型支持

每个 Microsoft Azure 服务都支持一个或多个静态加密模型。 但是，对于某些服务来说，其中的一个或多个加密模型可能并不适用。 对于支持客户管理的密钥方案的服务，它们可能只支持 Azure Key Vault 支持用于密钥加密密钥的密钥类型的一个子集。 另外，服务可能会按不同的计划发布对这些方案和密钥类型的支持。 此部分介绍的静态加密支持在撰写本文时仍适用于每个主要的 Azure 数据存储服务。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密

任何使用 Azure 基础结构即服务 (IaaS) 功能的客户都可以通过 Azure 磁盘加密为其 IaaS VM 和磁盘实施静态加密。 有关 Azure 磁盘加密的详细信息，请参阅 [Azure 磁盘加密文档](../azure-security-disk-encryption-overview.md)。

#### <a name="azure-storage"></a>Azure 存储

所有 Azure 存储服务（Blob 存储、队列存储、表存储和 Azure 文件存储）均支持静态服务器端加密，其中某些服务额外支持客户管理的密钥和客户端加密。

- 服务器端：默认情况下，所有 Azure 存储服务都使用服务托管的密钥来启用服务器端加密（对应用程序而言是透明的）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../../storage/common/storage-service-encryption.md)。 Azure Blob 存储和 Azure 文件也支持 Azure Key Vault 中客户托管的 RSA 2048 位密钥。 有关详细信息，请参阅 [Azure Key Vault 中使用客户托管密钥的存储服务加密](../../storage/common/storage-encryption-keys-portal.md)。
- 客户端：Azure Blob、表和队列支持客户端加密。 使用客户端加密时，客户会加密数据并将数据作为加密的 blob 上传。 密钥管理由客户执行。 有关详细信息，请参阅 [Microsoft Azure 存储的客户端加密和 Azure Key Vault](../../storage/common/storage-client-side-encryption.md)。

#### <a name="azure-sql-database"></a>Azure SQL 数据库

Azure SQL 数据库目前支持将静态加密用于 Microsoft 托管的服务器端和客户端加密方案。

对服务器加密的支持目前通过名为“透明数据加密”的 SQL 功能来提供。 在 Azure SQL 数据库客户启用 TDE 后，系统会自动为其创建和管理密钥。 可以在数据库和服务器级别启用静态加密。 从 2017 年 6 月开始，会在新创建的数据库上默认启用[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)。 Azure SQL 数据库支持 Azure Key Vault 中客户管理的 RSA 2048 位密钥。 有关详细信息，请参阅[使用 Azure SQL 数据库和数据仓库的“创建自己的密钥”支持进行透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)。

可以通过 [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 功能启用对 Azure SQL 数据库数据的客户端加密。 Always Encrypted 使用由客户端创建和存储的密钥。 客户可以将主密钥存储在 Windows 证书存储、Azure Key Vault 或本地硬件安全模块中。 使用 SQL Server Management Studio 时，SQL 用户可以选择想要使用什么密钥来加密哪个列。

## <a name="conclusion"></a>结论

保护存储在 Azure 服务中的客户数据对于 Microsoft 来说至关重要。 所有 Azure 托管服务都会始终提供静态加密选项。 Azure 服务支持服务管理的密钥、客户管理的密钥或客户端加密。 Azure 服务正在大范围地增强静态加密的可用性，计划在将来数月中推出新功能的预览版和公开发行版。

## <a name="next-steps"></a>后续步骤

- 有关服务托管密钥和客户托管密钥的详细信息，请参阅 [数据加密模型](encryption-models.md) 。
- 了解 Azure 如何使用 [双加密](double-encryption.md) 来缓解数据加密所带来的威胁。
