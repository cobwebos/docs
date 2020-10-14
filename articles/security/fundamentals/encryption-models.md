---
title: Microsoft Azure 中的数据加密模型
description: 本文概述了 Microsoft Azure 中的数据加密模型。
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
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 7b92c84234432320aa08017a15fbf8a5a4630eb3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019726"
---
# <a name="data-encryption-models"></a>数据加密模型

了解各种加密模型及其优缺点很重要，有助于了解 Azure 中的各个资源提供程序是如何实施静态加密的。 这些定义在 Azure 的所有资源提供程序中是共享的，目的是确保共同的语言和分类。

服务器端加密有三种方案：

- 使用服务托管密钥进行服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - Microsoft 管理密钥
  - 完整云功能

- 使用 Azure Key Vault 中客户托管密钥的服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - 客户通过 Azure Key Vault 控制密钥
  - 完整云功能

- 使用客户所控制硬件上的客户托管密钥的服务器端加密
  - Azure 资源提供程序执行加密和解密操作
  - 客户控制其所控制的硬件上的密钥
  - 完整云功能

服务器端加密模型是指由 Azure 服务执行的加密。 在该模型中，资源提供程序执行加密和解密操作。 例如，Azure 存储可能会以纯文本操作方式接收数据，并且会在内部进行加密和解密。 资源提供程序可能使用由 Microsoft 或客户管理的加密密钥，具体取决于提供的配置。

![服务器](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

每个服务器端静态加密模型都暗含密钥管理的独特特征。 其中包括：加密密钥的创建和存储位置和方式，以及访问模型和密钥轮换过程。  

对于客户端加密，请注意以下事项：

- Azure 服务无法看到已解密的数据
- 客户在本地（或其他安全存储中）管理和存储密钥。 Azure 服务无法使用密钥
- 精简云功能

Azure 中支持的加密模型分为两大类：“客户端加密”和“服务器端加密”，如前所述。 Azure 服务始终建议使用独立于所用静态加密模型的安全传输（例如 TLS 或 HTTPS）。 因此，传输过程中的加密应由传输协议来处理，不应成为决定要使用的静态加密模型的主要因素。

## <a name="client-encryption-model"></a>客户端加密模型

客户端加密模型是指由服务或调用应用程序在资源提供程序或 Azure 外部执行的加密。 加密可以由 Azure 中的服务应用程序执行，也可以由在客户数据中心运行的应用程序执行。 不管哪种情况，在采用此加密模型时，Azure 资源提供程序都会收到加密的数据 blob，但却无法以任何方式解密数据，也无法访问加密密钥。 在此模型中，密钥管理由调用服务/应用程序执行，对 Azure 服务来说是不透明的。

![客户端](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>使用服务托管密钥的服务器端加密

对许多客户来说，基本要求就是确保数据在静态时能够获得加密。 使用服务托管密钥的服务器端加密实现该模型的方式是：让客户标出适用于加密的特定资源（存储帐户、SQL DB 等），将所有密钥管理事项（例如密钥的颁发、轮换和备份）留给 Microsoft。 大多数支持静态加密的 Azure 服务通常都支持此模型，将加密密钥的管理工作卸载到 Azure。 Azure 资源提供程序创建密钥，将其置于安全的存储中，然后根据需要对其进行检索。 这意味着，服务具有密钥的完全访问权限，且服务可以全权控制凭据生命周期管理。

![托管式](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

因此，使用服务托管密钥的服务器端加密可以快速满足进行静态加密并降低客户开销的需求。 在可用的情况下，客户通常会打开适用于目标订阅和资源提供程序的 Azure 门户，选中一个表明其希望数据加密的复选框。 在某些资源管理器中，使用服务托管密钥的服务器端加密默认处于启用状态。

使用 Microsoft 托管密钥的服务器端加密意味着服务对存储具有完全访问权限，并且可以管理密钥。 某些客户可能希望对密钥进行管理，因为他们觉得自己可以获得更高的安全性，但在评估此模型时，应该考虑与自定义密钥存储解决方案相关联的成本和风险。 在许多情况下，组织可能觉得本地解决方案的资源约束或风险会大于对静态加密密钥进行云管理的风险。  但是，此模型可能满足不了某些组织的需求，这些组织需要控制加密密钥的创建或生命周期，或者需要安排某个人来管理服务，安排另一个人来管理该服务的加密密钥（也就是说，这是一种将密钥管理与总体管理分开的服务管理模型）。

### <a name="key-access"></a>密钥访问权限

使用服务托管密钥的服务器端加密在使用时，密钥的创建、存储和服务访问均由服务管理。 通常情况下，基础 Azure 资源提供程序会将数据加密密钥存储在靠近数据且能快速使用和访问的存储中，而将密钥加密密钥存储在安全的内部存储中。

**优点**

- 安装简单
- Microsoft 管理密钥轮换、备份和冗余
- 客户没有与实施相关联的成本，也没有自定义密钥管理方案的风险。

**缺点**

- 客户无法控制加密密钥（密钥规范、生命周期、吊销等）
- 此服务的管理模型无法将密钥管理与总体管理分开

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>使用 Azure Key Vault 中客户托管密钥的服务器端加密

对于需要加密静态数据并控制加密密钥的情况，客户可以选择使用 Key Vault 中客户托管密钥的服务器端加密。 某些服务可能仅将根密钥加密密钥存储在 Azure Key Vault 中，而将加密的数据加密密钥存储在更靠近数据的内部位置。 在这种情况下，客户可以将自己的密钥带到 Key Vault 中（BYOK – 自带密钥），或者生成新的密钥，以便加密所需资源。 资源提供程序在执行加密和解密操作时，会将所配置的密钥加密密钥用作所有加密操作的根密钥。

密钥加密密钥丢失意味着数据丢失。 因此，不应删除密钥。 每次创建或轮换时都应备份密钥。 应在存储着密钥加密密钥的任何保管库上启用[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。 不应删除密钥，而应将“启用”设置为 false 或设置到期日期。

### <a name="key-access"></a>密钥访问权限

将客户托管密钥置于 Azure Key Vault 中的服务器端加密模型涉及到的操作是，服务会根据需要访问用于加密和解密的密钥。 可以通过访问控制策略来允许服务访问静态加密密钥。 此策略授予服务标识接收密钥所需的访问权限。 可以为代表关联的订阅运行的 Azure 服务配置一个该订阅中的标识。 该服务可以执行 Azure Active Directory 身份验证，然后会收到一个身份验证令牌，将服务本身标识为代表该订阅的服务。 然后，该服务可以将该令牌出示给 Key Vault，以便获取有权访问的密钥。

对于使用加密密钥的操作，可以为服务标识授予以下任何操作的访问权限：decrypt、encrypt、unwrapKey、wrapKey、verify、sign、get、list、update、create、import、delete、backup、restore。

若要获取用于加密或解密静态数据的密钥，服务标识（将由资源管理器服务实例在运行时充当）必须使用 UnwrapKey 来获取解密用的密钥，并在创建新密钥时使用 WrapKey 将密钥插入密钥保管库中。

>[!NOTE]
>有关 Key Vault 授权的更多详细信息，请参阅 [Azure Key Vault 文档](../../key-vault/general/secure-your-key-vault.md)中的“保护密钥保管库”页。

**优点**

- 完全控制使用的密钥–加密密钥在客户的 Key Vault 下由客户控制。
- 能够通过加密将多个服务转换成一个主服务
- 此服务的管理模型可以将密钥管理与总体管理分开
- 可以跨区域定义服务和密钥位置

**缺点**

- 客户全权负责密钥访问管理
- 客户全权负责密钥生命周期管理
- 额外的安装和配置开销

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>使用客户所控制硬件中的客户管理的密钥进行服务器端加密

某些 Azure 服务启用了“托管自己的密钥 (HYOK)”密钥管理模型。 当需要对静止的数据进行加密并在不受 Microsoft 控制的专有存储库中管理密钥时，此管理模式非常有用。 在此模型中，服务必须从外部站点检索密钥。 性能和可用性担保会受影响，并且配置更复杂。 另外，由于服务可以在加密和解密操作过程中访问 DEK，此模型的总体安全保证类似于密钥在 Azure Key Vault 中由客户托管的情况。  因此，此模型不适合大多数组织，除非该组织有特定的密钥管理要求。 由于这些限制，大多数 Azure 服务不支持在客户控制的硬件中使用服务器托管密钥的服务器端加密。

### <a name="key-access"></a>密钥访问权限

当使用客户控制的硬件中的服务托管密钥的服务器端加密时，这些密钥将保留在由客户配置的系统上。 支持此模型的 Azure 服务提供了一种建立安全连接的方法，用于连接到客户提供的密钥存储。

**优点**

- 全权控制所用的根密钥 – 加密密钥由客户提供的存储托管
- 能够通过加密将多个服务转换成一个主服务
- 此服务的管理模型可以将密钥管理与总体管理分开
- 可以跨区域定义服务和密钥位置

**缺点**

- 全权负责密钥的存储、安全、性能和可用性
- 全权负责密钥访问管理
- 全权负责密钥生命周期管理
- 极高的安装、配置和持续维护成本
- 增强了对客户数据中心和 Azure 数据中心之间网络可用性的依赖。

## <a name="supporting-services"></a>支持服务
支持每个加密模型的 Azure 服务：

| 产品、功能或服务 | 使用服务托管密钥的服务器端   | 使用客户管理的密钥的服务器端 | 使用客户端管理的密钥的客户端  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI 和机器学习**      |                    |                    |                    |
| Azure 认知搜索           | 是                | 是                | -                  |
| Azure 认知服务         | 是                | 是                | -                  |
| Azure 机器学习           | 是                | 是                | -                  |
| Azure 机器学习工作室（经典） | 是         | 预览，RSA 2048 位 | -               |
| 内容审查器                | 是                | 是                | -                  |
| 人脸                             | 是                | 是                | -                  |
| 语言理解           | 是                | 是                | -                  |
| 个性化体验创建服务                     | 是                | 是                | -                  |
| QnA Maker                        | 是                | 是                | -                  |
| 语音服务                  | 是                | 是                | -                  |
| 文本翻译                  | 是                | 是                | -                  |
| Power BI                         | 是                | 是，RSA 4096 位  | -                  |
| **分析**                    |                    |                    |                    |
| Azure 流分析           | 是                | 暂无\*              | -                  |
| 事件中心                       | 是                | 是                | -                  |
| 函数                        | 是                | 是                | -                  |
| Azure Analysis Services          | 是                | -                  | -                  |
| Azure 数据目录               | 是                | -                  | -                  |
| Azure HDInsight                  | 是                | 全部                | -                  |
| Azure Monitor Application Insights | 是                | 是                | -                  |
| Azure Monitor Log Analytics      | 是                | 是                | -                  |
| Azure 数据资源管理器              | 是                | 是                | -                  |
| Azure 数据工厂               | 是                | 是                | -                  |
| Azure Data Lake Store            | 是                | 是，RSA 2048 位  | -                  |
| **容器**                   |                    |                    |                    |
| Azure Kubernetes 服务         | 是                | 是                | -                  |
| 容器实例              | 是                | 是                | -                  |
| 容器注册表               | 是                | 是                | -                  |
| **计算**                      |                    |                    |                    |
| 虚拟机                 | 是                | 是                | -                  |
| 虚拟机规模集        | 是                | 是                | -                  |
| SAP HANA                         | 是                | 是                | -                  |
| 应用服务                      | 是                | 是\*\*            | -                  |
| 自动化                       | 是                | 是\*\*            | -                  |
| Azure Functions                  | 是                | 是\*\*            | -                  |
| Azure 门户                     | 是                | 是\*\*            | -                  |
| 逻辑应用                       | 是                | 是                | -                  |
| Azure 托管应用程序       | 是                | 是\*\*            | -                  |
| 服务总线                      | 是                | 是                | -                  |
| 站点恢复                    | 是                | 是                | -                  |
| **数据库**                    |                    |                    |                    |
| 虚拟机上的 SQL Server   | 是                | 是                | 是                |
| Azure SQL 数据库               | 是                | 是，RSA 3072 位  | 是                |
| Azure SQL Database for MariaDB   | 是                | -                  | -                  |
| Azure SQL Database for MySQL     | 是                | 是                | -                  |
| Azure SQL Database for PostgreSQL | 是               | 是                | -                  |
| Azure Synapse Analytics          | 是                | 是，RSA 3072 位  | -                  |
| SQL Server Stretch Database      | 是                | 是，RSA 3072 位  | 是                |
| 表存储                    | 是                | 是                | 是                |
| Azure Cosmos DB                  | 是                | 是                | -                  |
| Azure Databricks                 | 是                | 是                | -                  |
| Azure 数据库迁移服务 | 是                | 暂无\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | 是                | -                  | 是                |
| Azure Repos                      | 是                | -                  | 是                |
| **标识**                     |                    |                    |                    |
| Azure Active Directory           | 是                | -                  | -                  |
| Azure Active Directory 域服务 | 是          | 是                | -                  |
| **集成**                  |                    |                    |                    |
| 服务总线                      | 是                | 是                | 是                |
| 事件网格                       | 是                | -                  | -                  |
| API 管理                   | 是                | -                  | -                  |
| IoT 服务****                 |                    |                    |                    |
| IoT 中心                          | 是                | 是                | 是                |
| IoT 中心设备预配      | 是                | 是                | -                  |
| **管理和治理**    |                    |                    |                    |
| Azure Site Recovery              | 是                | -                  | -                  |
| Azure Migrate                    | 是                | 是                | -                  |
| **介质**                        |                    |                    |                    |
| 媒体服务                   | 是                | -                  | 是                |
| **安全性**                     |                    |                    |                    |
| 适用于 IoT 的 Azure 安全中心    | 是                | 是                | -                  |
| Azure Sentinel                   | 是                | 是                | -                  |
| **存储**                      |                    |                    |                    |
| Blob 存储                     | 是                | 是                | 是                |
| 高级 Blob 存储             | 是                | 是                | 是                |
| 磁盘存储                     | 是                | 是                | -                  |
| 超级磁盘存储               | 是                | 是                | -                  |
| 托管磁盘存储             | 是                | 是                | -                  |
| 文件存储                     | 是                | 是                | -                  |
| 文件高级存储             | 是                | 是                | -                  |
| 文件同步                        | 是                | 是                | -                  |
| 队列存储                    | 是                | 是                | 是                |
| Avere vFXT                       | 是                | -                  | -                  |
| 用于 Redis 的 Azure 缓存            | 是                | 暂无\*              | -                  |
| Azure NetApp 文件               | 是                | 是                | -                  |
| 存档存储                  | 是                | 是                | -                  |
| StorSimple                       | 是                | 是                | 是                |
| Azure 备份                     | 是                | 是                | 是                |
| Data Box                         | 是                | -                  | 是                |
| Data Box Edge                    | 是                | 是                | -                  |

\* 此服务不会持久保存数据。 将使用 Microsoft 密钥来加密暂时性缓存（如果有）。

\*\* 此服务支持将数据存储在你自己的 Key Vault、存储帐户或其他已支持使用客户管理的密钥进行服务器端加密的数据持久保存服务中。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在 Azure 中使用加密](encryption-overview.md)。
- 了解 Azure 如何使用 [双加密](double-encryption.md) 来缓解数据加密所带来的威胁。
