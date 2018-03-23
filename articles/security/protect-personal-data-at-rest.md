---
title: Azure 利用加密保护个人静态数据 | Microsoft Docs
description: 本文可用于符合行业或政府的要求，如一般保护数据条例 (GDPR)。
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 37da3d91df4be871d6647b6290768ea4999a5010
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure 加密技术：使用加密保护个人静态数据

本文帮助读者了解 Azure 加密技术保护以及如何使用它来保护静态数据。

静态数据的加密至关重要，它是保护敏感数据或个人数据，满足法规遵从和数据隐私要求的最佳做法。 静态加密旨在防止攻击者访问未加密的数据，其方法是确保这些数据在磁盘上时是加密的。 本文中包含的信息可用于遵守一般数据保护条例 (GDPR)。

## <a name="scenario"></a>场景 

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海和波罗的海以及英属岛屿提供路线。 为支持这些工作，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。

该公司使用 Microsoft Azure 在云中存储企业数据。 这可能包括如下所述的客户和/或员工信息：

- 地址
- 电话号码
- 税务识别号
- 信用卡信息

该公司必须保护客户和员工数据的隐私，同时，可让相应的部门在有需要时访问数据。 （例如薪酬和预订部门）

该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库，其中包含用于跟踪与当前和既往客户之间的关系的个人信息。

### <a name="problem-statement"></a>问题陈述

该公司必须保护客户和员工个人数据的隐私，同时，可让相应的部门（例如薪酬和预订部门）在有需要时访问数据。 这些个人数据存储在企业控制的数据中心外部，但不受公司的物理控制。

### <a name="company-goal"></a>公司目标

作为多层深层防御安全策略的一部分，公司的目标是确保包含个人数据的所有数据源（包括驻留在云存储中的数据源）经过加密。 如果未经授权的人员获取了个人数据的访问权限，必须以不可读的形式呈现数据。 对于用户和管理员而言，应用加密的过程应该简单或透明。

## <a name="solutions"></a>解决方案

Azure 服务提供多种工具和技术来帮助通过加密保护个人静态数据。

### <a name="azure-key-vault"></a>Azure 密钥保管库

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 为在 Azure 服务中加密静态数据所用的密钥提供安全存储，是建议的密钥存储和管理解决方案。 加密密钥管理对保护存储的数据至关重要。

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>如何使用 Azure Key Vault 来保护用于加密个人数据的密钥？

若要使用 Azure Key Vault，需要 Azure 帐户的订阅。 此外，还需要安装 Azure PowerShell。 步骤包括使用 PowerShell cmdlet 来执行以下操作：

1. 连接到订阅

2. 创建 key vault

3. 将密钥或密码添加到密钥保管库

4. 将要使用 Key Vault 的应用程序注册到 Azure Active Directory

5. 授权应用程序使用密钥或机密

若要创建 Key Vault，请使用 New-AzureRmKeyVault PowerShell CmDlt。 需分配保管库名称、资源组名称和地理位置。 通过其他 Cmdlet 管理密钥时，需使用保管库名称。 通过 REST API 使用保管库的应用程序将使用保管库 URI。

Azure Key Vault 可提供软件保护的密钥，或者你可以导入 .PFX 文件中的现有密钥。 可在保管库中存储机密（密码）。

还可以在本地 HSM 中生成密钥，并将其传输到 Key Vault 服务中的 HSM，而密钥不会超出 HSM 界限。

有关使用 Azure Key Vault 的详细的说明，请遵循 [Azure Key Vault 入门](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)中的步骤。

有关可对 Azure Key Vault 使用的 PowerShell Cmdlet 的列表，请参阅 [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)。

### <a name="azure-disk-encryption-for-windows"></a>适用于 Windows 的 Azure 磁盘加密

[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)保护 Azure 虚拟机上的个人静态数据，并与 Azure Key Vault 集成。 Azure 磁盘加密在 Windows 中使用 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx)、在 Linux 中使用 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 来加密 OS 磁盘和数据磁盘。 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 以及 Windows 8 和 Windows 10 客户端支持 Azure 磁盘加密。

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>如何使用 Azure 磁盘加密来保护个人数据？

若要使用 Azure 磁盘加密，需要 Azure 帐户的订阅。 若要为 Windows 和 Linux VM 启用 Azure 磁盘加密，请执行以下操作：

1. 使用 Azure 磁盘加密资源管理器模板、PowerShell 或命令行接口 (CLI) 启用磁盘加密，并指定加密配置。 

2. 授予 Azure 平台从 Key Vault 读取加密材料的访问权限。

3. 提供 Azure Active Directory (AAD) 应用程序标识，以便将加密密钥材料写入 Key Vault。

Azure 会更新 VM 和 Key Vault 配置，并设置加密的 VM。

将 Key Vault 设置为支持 Azure 磁盘加密时，可以添加密钥加密密钥 (KEK) 来提高安全性，并支持已加密虚拟机的备份。

![](media/protect-personal-data-at-rest/create-key.png)

[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)中提供了有关具体部署方案和用户体验的详细说明。

### <a name="azure-storage-service-encryption"></a>Azure 存储服务加密

[静态数据的 Azure 存储服务加密 (SSE)](https://docs.microsoft.com/azure/storage/storage-service-encryption) 可帮助保护数据，使组织能够信守在安全性与合规性方面所做的承诺。 Azure 存储使用 256 位 AES 加密法自动加密数据，再将数据持久保存到存储中，并在检索之前解密数据。 此服务适用于 Azure Blob 和文件。

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>如何使用存储服务加密来保护个人数据？

若要启用存储服务加密，请执行以下操作：

1. 登录到 Azure 门户。

2. 选择一个存储帐户。

3. 在“设置”中的“Blob 服务”部分下，选择“加密”。

4. 在“文件服务”部分下，选择“加密”。

单击“加密”设置后，可以启用或禁用存储服务加密。

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

新数据将会加密。 此存储帐户的现有文件中的数据会保持未加密状态。

启用加密之后，请使用以下方法之一将数据复制到存储帐户：

1. 使用 [AzCopy 命令行实用工具](https://docs.microsoft.com/azure/storage/storage-use-azcopy)复制 Blob 或文件。

2. [使用 SMB 装载文件共享](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows)，以便可以使用 Robocopy 等实用工具来复制文件。

3. 使用 [.NET 等存储客户端库](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)来与 Blob 存储来回复制或者在存储帐户之间复制 Blob 或文件数据。

4.  使用[存储资源管理器](https://docs.microsoft.com/azure/storage/storage-explorers)将 Blob 上传到已启用加密的存储帐户。

### <a name="transparent-data-encryption"></a>透明数据加密

透明数据加密 (TDE) 是 SQL Azure 中的一项功能，可用于在数据库和服务器级别加密数据。 现在，TDE 默认已在所有新建的数据库上启用。 TDE 针对数据和日志文件执行实时 I/O 加密和解密。

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>如何使用 TDE 来保护个人数据？

可以通过 Azure 门户、REST API 或 PowerShell 配置 TDE。 若要使用 Azure 门户在现有的数据库上启用 TDE，请执行以下操作：

1. 访问 Azure 门户 (<https://portal.azure.com>)，并使用自己的 Azure 管理员或参与者帐户登录。

2. 在左侧标题中单击“浏览”，并单击“SQL 数据库”。

3. 在左窗格中选择“SQL 数据库”后，请单击自己的用户数据库。

4. 在数据库边栏选项卡中，单击“所有设置”。

5. 在“设置”边栏选项卡中，单击“透明数据加密”部分打开“透明数据加密”边栏选项卡。

6. 在“数据加密”边栏选项卡中，将“数据加密”按钮切换为“打开”，并单击“保存”（页面顶部）以应用设置。 “加密状态”会显示透明数据加密的大致进度。

![启用数据加密](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

[对 Azure SQL 数据库使用透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)中介绍了有关如何启用 TDE 的说明，以及有关解密受 TDE 保护的数据库的信息和其他信息。

## <a name="summary"></a>摘要

公司可以实现对 Azure 云中存储的个人数据进行加密的目标。 为此，他们可以使用 Azure 磁盘加密来保护整个卷。 受保护的项可以包括操作系统文件，以及用于保存个人身份信息和其他敏感数据的数据文件。 可以使用 Azure 存储服务加密来保护 Blob 和文件中存储的个人数据。 对于 Azure SQL 数据库中存储的数据，透明数据加密可以防范在未经授权的情况下透露个人信息。

若要保护用于在 Azure 中加密数据的密钥，公司可以使用 Azure Key Vault。 这样就可以简化密钥管理过程，使公司能够保持控制用于访问和加密个人数据的密钥。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密故障排除指南](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [加密 Azure 虚拟机](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Azure Data Lake Store 中的数据加密](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Azure Cosmos DB 数据库静态加密](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
