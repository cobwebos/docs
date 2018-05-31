---
title: Azure 加密概述 | Microsoft Docs
description: 了解 Azure 中的各种加密选项
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 00c8b30b5351b7a6e4388b186fab70e3a3357ef4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366301"
---
# <a name="azure-encryption-overview"></a>Azure 加密概述

本文概述了如何在 Microsoft Azure 中使用加密。 其中涵盖了加密的主要领域，包括 Azure Key Vault 的静态加密、动态加密以及密钥管理。 每个部分包括更详细信息的链接。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

静态数据包括以任何数字格式驻留在物理介质上的永久性存储中的信息。 该介质可包括磁性介质或光学介质上的文件、归档数据和数据备份。 Microsoft Azure 提供各种数据存储解决方案，以满足不同需求，包括文件、磁盘、blob 和表存储。 Microsoft 还提供加密以保护 [Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)、[Azure Cosmos DB](../cosmos-db/introduction.md) 和 Azure Data Lake。

静态数据加密可用于服务型软件 (SaaS)、平台即服务 (PaaS) 和服务架构 (IaaS) 云模型中的服务。 本文总结并提供资源，以帮助使用 Azure 加密选项。

有关如何在 Azure 中加密静态数据的更详细的讨论，请参阅 [Azure 静态数据加密](azure-security-encryption-atrest.md)。

## <a name="azure-encryption-models"></a>Azure 加密模型

Azure 支持各种加密模型，包括使用服务托管密钥、Key Vault 中客户托管密钥或客户所控硬件上客户托管密钥的服务器端加密。 通过客户端加密，可在本地或另一个安全位置管理并存储密钥。

### <a name="client-side-encryption"></a>客户端加密

客户端加密在 Azure 之外执行。 其中包括：

- 由客户数据中心中运行的应用程序或服务应用程序加密的数据。
- 当 Azure 接收到数据时，数据已加密。

使用客户端加密时，云服务提供商无法访问加密密钥，因此无法解密该数据。 你完全控制了密钥。

### <a name="server-side-encryption"></a>服务器端加密

三个服务器端加密模型提供不同的密钥管理特性，可根据要求进行选择：

- **服务托管密钥：** 可带来低开销的控制和便利。

- **客户托管密钥**：可用于控制密钥，包括支持“创建自己的密钥”(BYOK) 或生成新密钥。

- **客户所控硬件上的服务托管密钥**：可用于管理不受 Microsoft 控制的专有存储库中的密钥。 此特性称为自留密钥 (HYOK)。 但是，配置相当复杂，并且大多数 Azure 服务都不支持此模式。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密

可使用 [Azure 磁盘加密](azure-security-disk-encryption.md)保护 Windows 和 Linux 虚拟机，它采用 [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) 技术和 Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)通过全卷加密来保护操作系统磁盘和数据磁盘。

[Azure Key Vault 订阅](../key-vault/key-vault-whatis.md)中的加密密钥和机密会得到保护。 使用 Azure 备份服务，可备份和还原使用密钥加密密钥 (KEK) 配置的加密虚拟机 (VM)。

### <a name="azure-storage-service-encryption"></a>Azure 存储服务加密

Azure Blob 存储和 Azure 文件共享中的静态数据都可以在服务器端和客户端方案中进行加密。

[Azure 存储服务加密 (SSE)](../storage/common/storage-service-encryption.md) 可在数据存储前自动加密数据，并在检索数据时自动解密数据。 此过程对用户是完全透明的。 存储服务加密使用 256 位[高级加密标准 (AES) 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是可用的最强分组加密中的一种。 AES 采用透明方式处理加密、解密和密钥管理。

### <a name="client-side-encryption-of-azure-blobs"></a>Azure blob 的客户端加密

可通过各种方式执行 Azure blob 的客户端加密。

可使用适用于 .NET NuGet 包的 Azure 存储客户端库在客户端应用程序内加密数据，然后再将其上传到 Azure 存储。

若要详细了解和下载适用于.NET NuGet 包的 Azure 存储客户端库，请参阅 [Windows Azure 存储 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)。

在 Key Vault 中使用客户端加密时，将使用 Azure 存储客户端 SDK 生成的一次性对称内容加密密钥 (CEK) 加密数据。 CEK 在使用密钥加密密钥 (KEK) 加密后，可以是对称密钥，也可以是非对称密钥对。 可在本地进行管理或将其存储在 Key Vault 中。 然后，将加密数据上传到 Azure 存储。

若要详细了解 Key Vault 的客户端加密和学习使用操作说明，请参阅[教程：在 Azure 存储中使用 Key Vault 加密和解密 Blob](../storage/storage-encrypt-decrypt-blobs-key-vault.md)。

最后，还可使用适用于 Java 的 Azure 存储客户端库执行客户端加密，之后再将数据上传到 Azure 存储，并在将数据下载到客户端时解密数据。 此库还支持与 [Key Vault](https://azure.microsoft.com/services/key-vault/) 集成，以便管理存储帐户密钥。

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>使用 Azure SQL 数据库加密静态数据

[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)是 Azure 中通用的关系数据库服务，支持关系数据、JSON、空间和 XML 等结构。 SQL 数据库通过透明数据加密 (TDE) 功能支持服务器端加密，通过 Always Encrypted 功能支持客户端加密。

#### <a name="transparent-data-encryption"></a>透明数据加密

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) 可通过数据库加密密钥 (DEK) 实时加密 [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016)、[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)和 [Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)数据文件，该加密密钥存储在数据库启动记录中，可在恢复期间使用。

TDE 使用 AES 和三重数据加密标准 (3DES) 加密算法保护数据和日志文件。 数据库文件加密在页面级执行。 加密数据库中的页面在写入磁盘之前被加密，在读入内存后被解密。 默认情况下，新创建的 Azure SQL 数据库启用 TDE。

#### <a name="always-encrypted-feature"></a>Always Encrypted 功能

借助 Azure SQL 中的 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 功能，可在客户端应用程序中加密数据，之后再将其存储在 Azure SQL 数据库中。 还可将本地数据库管理工作委派给第三方，并将数据拥有者和可查看数据的人员，以及管理数据但无权访问数据的人员分开。

#### <a name="cell-level-or-column-level-encryption"></a>单元级加密或列级加密

借助 Azure SQL 数据库，可使用 Transact-SQL 对数据列应用对称加密。 这种方法被称为[单元级加密或列级加密 (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data)，因为可使用这种方法通过不同加密密钥来加密特定列，甚至是特定数据单元。 这样可以提供比 TDE 更加精细的加密功能，能够加密页面中的数据。

CLE 具有内置函数，可通过函数使用对称或非对称密钥、证书的公钥或 3DES 的密码来加密数据。

### <a name="cosmos-db-database-encryption"></a>Cosmos DB 数据库加密

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) 由 Microsoft 提供，是全球分布式多模型数据库。 存储在非易失性存储（固态硬盘）中的 Cosmos DB 中的用户数据默认加密。 无法将其打开或关闭。 静态加密是通过许多安全技术实现的，其中包括安全密钥存储系统、加密网络以及加密 API。 加密密钥由 Microsoft 管理，并根据 Microsoft 的内部指南进行轮换。

### <a name="at-rest-encryption-in-data-lake"></a>Data Lake 中的静态加密

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) 是在正式定义需求或架构之前，在单个位置收集的每种类型数据的企业级存储库。 Data Lake Store 支持“默认启用”透明加密静态数据，可以在创建帐户期间设置。 默认情况下，Azure Data Lake Store 替你管理密钥，但你可以选择自己管理密钥。

有三种类型的密钥用于加密和解密数据：主加密密钥 (MEK)、数据加密密钥 (DEK) 和块加密密钥 (BEK)。 MEK 用于加密存储在永久性介质上的 DEK，BEK 派生自 DEK 和数据块。 如果管理自己的密钥，可以轮换 MEK。

## <a name="encryption-of-data-in-transit"></a>传输中的数据加密

Azure 提供了许多机制，用于在迁移数据时保持数据的私密性。

### <a name="tlsssl-encryption-in-azure"></a>Azure 中的 TLS/SSL 加密

Microsoft 使用[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 协议，在云服务和客户之间传输数据时提供保护。 Microsoft 的数据中心与连接到 Azure 服务的客户端系统协商建立 TLS 连接。 TLS 提供严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。

[完美正向保密](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) 通过唯一密钥保护客户的客户端系统与 Microsoft 云服务间的连接。 连接还使用基于 RSA 的 2,048 位加密密钥长度。 此组合使得别人难以拦截和访问传输中的数据。

### <a name="azure-storage-transactions"></a>Azure 存储事务

当通过 Azure 门户与 Azure 存储交互时，所有事务都通过 HTTPS 发生。 也可根据 HTTPS 使用存储 REST API 与 Azure 存储交互。 在调用 REST API 来访问存储帐户中的对象时，可通过启用存储帐户所需的安全传输来强制使用 HTTPS。

使用共享访问签名 ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)) 除了能委派对 Azure 存储对象的访问权限，还能包含一个选项，指定在使用共享访问签名时只能使用 HTTPS 协议。 通过此方法，可确保只能使用正确的协议发送有 SAS 令牌的链接。

用于访问 Azure 文件共享的 [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) 支持加密，并且可以在 Windows Server 2012 R2、Windows 8、Windows 8.1 和 Windows 10 中使用。 它允许跨区域访问，甚至在桌面上访问。

在将数据发送到 Azure 存储实例前，客户端加密会对数据加密，所以在通过网络传输时数据是加密的。

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure 虚拟网络上的 SMB 加密 

在运行 Windows Server 2012 或更高版本的 VM 中使用 [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) 后，可对在 Azure 虚拟网络上传输数据进行加密，以此确保数据安全传输。 加密数据有助于防止数据遭到篡改和窃听攻击。 管理员可以为整个服务器启用 SMB 加密，也可以启用特定的共享。

默认情况下，为共享或服务器启用 SMB 加密后，只允许 SMB 3.0 客户端访问加密共享。

## <a name="in-transit-encryption-in-vms"></a>VM 中的传输中加密

根据连接的性质，通过多种方式对在运行 Windows 的 VM 间传输的数据进行加密。

### <a name="rdp-sessions"></a>RDP 会话

可以使用 Windows 客户端计算机或者安装了 RDP 客户端的 Mac 上的[远程桌面协议 (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) 连接并登录 VM。 在 RDP 会话中通过网络传输的数据可以受到 TLS 的保护。

还可使用远程桌面连接到 Azure 中的 Linux VM。

### <a name="secure-access-to-linux-vms-with-ssh"></a>使用 SSH 安全访问 Linux VM

若要进行远程管理，可以使用[安全外壳](../virtual-machines/linux/ssh-from-windows.md) (SSH) 连接到在 Azure 中运行的 Linux VM。 SSH 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 它是在 Azure 中托管的 Linux VM 的默认连接协议。 使用 SSH 密钥进行身份验证，无需使用密码即可登录。 SSH 使用公钥/私钥对（非对称加密）进行身份验证。

## <a name="azure-vpn-encryption"></a>Azure VPN 加密

连接到 Azure 时可以使用虚拟专用网络，它可以创建安全通道以保护通过网络发送的数据的隐私。

### <a name="azure-vpn-gateways"></a>Azure VPN 网关

可使用 [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)跨公共连接在虚拟网络和本地位置间发送加密流量，或在虚拟网络间发送流量。

站点到站点 VPN 使用 [IPsec](https://en.wikipedia.org/wiki/IPsec) 进行传输加密。 Azure VPN网关使用一组默认提议。 可将 Azure VPN 网关配置为使用具有特定加密算法和关键优势的自定义 IPsec/IKE 策略，而不是 Azure 默认策略集。

### <a name="point-to-site-vpns"></a>点到站点 VPN

点到站点 VPN 允许单个客户端计算机访问 Azure 虚拟网络。 [安全套接字隧道协议 (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) 可用于创建 VPN 隧道。 它可遍历防火墙（隧道显示为 HTTPS 连接）。 你可使用自己的内部公钥基础结构 (PKI) 根证书颁发机构 (CA) 实现点到站点的连接。

可以使用具有证书身份验证或 PowerShell 的 Azure 门户，将点到站点 VPN 连接配置到虚拟网络。

若要详细了解点到站点 VPN 连接与 Azure 虚拟网络，请参阅：

[使用证书身份验证将点到站点连接配置到虚拟网络：Azure 门户](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[使用证书身份验证将点到站点连接配置到虚拟网络：PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>站点到站点 VPN 

可使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接需要一个分配有面向外部的公共 IP 地址的本地 VPN 设备。

可以使用 Azure门户、PowerShell 或 Azure CLI 将站点到站点 VPN 连接配置到虚拟网络。

有关详细信息，请参阅：

[在 Azure 门户中创建站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[在 PowerShell 门户中创建站点到站点连接](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[使用 CLI 创建具有站点到站点 VPN 连接的虚拟网络](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Data Lake 中的传输中加密

此外，还会始终在 Data Lake Store 中对传输数据（也称移动数据）加密。 除在将数据存储到永久性介质前进行加密外，还始终会在数据传输过程中使用 HTTPS 保护数据。 HTTPS 是 Data Lake Store REST 接口唯一支持的协议。

若要详细了解 Data Lake 中的传输中数据加密，请参阅 [Data Lake Store 中的数据加密](../data-lake-store/data-lake-store-encryption.md)。

## <a name="key-management-with-key-vault"></a>使用 Key Vault 的密钥管理

如果没有适当地保护和管理密钥，加密会变得毫无用处。 Key Vault 是 Microsoft 推荐的解决方案，用于管理和控制云服务使用的对加密密钥的访问。 访问密钥的权限可以通过 Azure Active Directory 帐户分配给服务或用户。

Key Vault 可帮助组织减少对配置、修补以及维护硬件安全模块 (HSM) 和密钥管理软件的需求。 使用 Key Vault 时，一切由你控制。 Microsoft 永远看不到你的密钥，应用程序无法直接访问密钥。 也可以在 HSM 中导入或生成密钥。

## <a name="next-steps"></a>后续步骤

- [Azure 安全概述](security-get-started-overview.md)
- [Azure 网络安全概述](security-network-overview.md)
- [Azure 数据库安全性概述](azure-database-security-overview.md)
- [Azure 虚拟机安全概述](security-virtual-machines-overview.md)
- [静态数据加密](azure-security-encryption-atrest.md)
- [数据安全与加密最佳实践](azure-security-data-encryption-best-practices.md)
