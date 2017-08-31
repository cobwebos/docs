---
title: "Azure 加密概述 | Microsoft Docs"
description: "了解 Azure 中的各种加密选项"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---
# <a name="azure-encryption-overview"></a>Azure 加密概述

本文概述了如何在 Microsoft Azure 中使用加密。 涵盖了加密的主要领域，包括 Key Vault 的静态加密、动态加密以及密钥管理。 每个部分包括更详细信息的链接。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

静态数据包括以任何数字格式驻留在物理介质上的永久性存储中的信息。 其中包括磁性介质或光学介质上的文件、归档数据和数据备份。 Microsoft Azure 提供各种数据存储解决方案，以满足不同需求，包括文件、磁盘、blob 和表存储。 Microsoft 还提供加密以保护 [Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)、[CosmosDB](../cosmos-db/introduction.md) 和 Azure Data Lake。

静态数据加密可用于 Azure 软件即服务 (SaaS)、平台即服务 (PaaS) 和基础结构即服务 (IaaS) 云模型中的服务。 本文总结并提供资源，以帮助使用 Azure 的加密选项。

有关如何在 Azure 中加密静态数据的更详细的讨论，请参阅标题为 [Azure 静态数据加密](azure-security-encryption-atrest.md)的文档

## <a name="azure-encryption-models"></a>Azure 加密模型

Azure 支持各种加密模型，包括使用服务托管密钥的服务器端加密、在 Azure Key Vault 中使用客户托管的密钥或在客户控制的硬件上使用客户托管的密钥。 通过客户端加密，可在本地或另一个安全位置管理并存储密钥。

### <a name="client-side-encryption"></a>客户端加密

客户端加密在 Azure 之外执行。 客户端加密包括：

- 由客户数据中心运行的应用程序或服务应用程序加密的数据
- 当 Azure 接收到数据时，数据已加密。

使用客户端加密时，云服务提供程序无法访问加密密钥，因此无法解密该数据。 你完全控制了密钥。

### <a name="server-side-encryption"></a>服务器端加密

三个服务器端加密模型提供不同的密钥管理特性，可根据要求进行选择。

- 服务托管密钥可以带来低开销的控制和便利

- 客户托管密钥可用于控制密钥，包括自带密钥 (BYOK) 或生成新密钥的功能。

- 客户所控硬件上的服务托管密钥可用于管理不受 Microsoft 控制的专有存储库中的密钥。 这称为自留密钥 (HYOK)。 但是，配置相当复杂，并且大多数 Azure 服务都不支持此模式。

### <a name="azure-disk-encryption"></a>Azure 磁盘加密

[Azure 磁盘加密](azure-security-disk-encryption.md)可用于保护 Windows 和 Linux 虚拟机，它采用 [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) 技术和 Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 来保护采用全卷加密的操作系统磁盘和数据磁盘。

[Azure Key Vault](../key-vault/key-vault-whatis.md) 订阅中的加密密钥和机密会得到保护。 可以使用 Azure 备份服务备份和还原使用 KEK 配置加密的加密 VM。

### <a name="azure-storage-service-encryption"></a>Azure 存储服务加密

Azure 存储中的静态数据（blob 和文件）都可以在服务器端和客户端方案中进行加密。

[Azure 存储服务加密](../storage/storage-service-encryption.md) (SSE) 可以在数据存储之前自动加密数据，并在检索数据时自动解密，此过程对用户完全透明。 存储服务加密使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，这是可用的最强分组加密之一，以透明的方式处理加密、解密和密钥管理。

### <a name="client-side-encryption-of-azure-blobs"></a>Azure blob 的客户端加密

Azure blob 的客户端加密可以采用不同的方式。

可使用适用于 .NET NuGet 包的 Azure 存储客户端库在客户端应用程序内加密数据，然后再将其上传到 Azure 存储。

若要详细了解和下载适用于.NET NuGet 包的 Azure 存储客户端库，请参阅标题为 [Windows Azure 存储 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage) 的文档

在 Azure Key Vault 中使用客户端加密时，将使用 Azure 存储客户端 SDK 生成的一次性对称内容加密密钥 (CEK) 加密数据。 CEK 在使用密钥加密密钥 (KEK) 加密后，可以是对称密钥，也可以是非对称密钥对。 可在本地进行管理或将其存储在 Azure Key Vault 中。 然后，将加密数据上传到 Azure 存储服务。

要详细了解 Azure Key Vault 的客户端加密和学习使用操作说明，请参阅标题为[教程：在 Microsoft Azure 存储中使用 Azure Key Vault 加密和解密 Blob](../storage/storage-encrypt-decrypt-blobs-key-vault.md) 的文档

最后，还可以使用适用于 Java 的 Azure 存储客户端库，在将数据上传到 Azure 存储之前执行客户端加密，并在将数据下载到客户端时解密数据。 此库还支持与 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 集成，以便管理存储帐户密钥。

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>使用 Azure SQL 数据库加密静态数据

[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)是 Microsoft Azure 中通用的关系数据库服务，支持关系数据、JSON、空间和 XML 等结构。 Azure SQL 通过透明数据加密 (TDE) 功能支持服务器端加密，通过 Always Encrypted 功能支持客户端加密。

#### <a name="transparent-data-encryption"></a>透明数据加密

[TDE 透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)用于通过数据库加密密钥 (DEK) 实时加密 [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016)、[Azure SQL 数据库](../sql-database/sql-database-technical-overview.md)和 [Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)数据文件，该加密密钥存储在数据库启动记录中，可在恢复期间使用。

TDE 使用 AES 和 3DES 加密算法保护数据和日志文件。 在页面级别加密数据库文件；加密数据库中的页面在写入磁盘之前被加密，在读入内存后被解密。 默认情况下，新创建的 Azure SQL 数据库启用 TDE。

#### <a name="always-encrypted"></a>Always Encrypted

通过 Azure SQL 中的 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 功能，能够在存储在 Azure SQL 数据库之前对客户端应用程序中的数据进行加密，能够将本地数据库管理委托给第三方，将拥有并能查看数据的一方与管理数据但不应该访问数据的一方隔离开来。

#### <a name="cellcolumn-level-encryption"></a>单元/列级加密

通过 Azure SQL 数据库能够使用 Transact-SQL 对数据列应用对称加密。 这被称为[单元级加密或列级加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE)，因为可以使用它来通过不同加密密钥加密特定列，甚至特定的数据单元。 这样可以提供比 TDE 更加精细的加密功能，能够加密页面中的数据。

CLE 具有内置函数，在加密数据时可使用对称或非对称密钥、使用证书的公钥或使用 3DES 的密码。

### <a name="cosmos-db-database-encryption"></a>Cosmos DB 数据库加密

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) 由 Microsoft 提供，是全球分布式多模型数据库。 存储在非易失性存储（固态硬盘）中的 Cosmos DB 中的用户数据默认加密；没有控制可以将其打开或关闭。 静态加密是通过许多安全技术实现的，其中包括安全密钥存储系统、加密网络以及加密 API。 加密密钥由 Microsoft 管理，并根据 Microsoft 的内部指南进行轮换。

### <a name="at-rest-encryption-in-azure-data-lake"></a>Azure Data Lake 中的静态加密

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) 是在正式定义需求或架构之前，在单个位置收集的每种类型数据的企业级存储库。 Azure Data Lake Store 支持“默认启用”透明加密静态数据，可以在创建帐户期间设置。 默认情况下，Data Lake Store 替你管理密钥，但你可以选择自己管理密钥。

有三种类型的密钥用于加密和解密数据：主加密密钥 (MEK)、数据加密密钥 (DEK) 和块加密密钥 (BEK)。 MEK 用于加密存储在永久性介质上的 DEK，BEK 派生自 DEK 和数据块。 如果管理自己的密钥，可以轮换 MEK。

## <a name="encryption-of-data-in-transit"></a>传输中的数据加密

Azure 提供了许多机制，用于在迁移数据时保持数据的私密性。

### <a name="tlsssl-encryption-in-azure"></a>Azure 中的 TLS/SSL 加密

Microsoft 使用[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 协议，在云服务和客户之间传输数据时提供保护。 Microsoft 的数据中心与连接到 Azure 服务的客户端系统协商建立 TLS 连接。 TLS 进行严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。

[完美正向保密](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) 通过唯一密钥保护客户的客户端系统与 Microsoft 云服务之间的连接。 连接还使用基于 RSA 的 2,048 位加密密钥长度。 此组合使得别人难以拦截和访问传输中的数据。

### <a name="azure-storage-transactions"></a>Azure 存储事务

当通过 Azure 门户与 Azure 存储交互时，所有事务都通过 HTTPS 发生。 也可根据 HTTPS 使用存储 REST API 与 Azure 存储交互。 在调用 REST API 来访问存储帐户中的对象时，可以通过为存储帐户启用需要安全传输来强制使用 HTTPS。

可以使用共享访问签名 ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md))，它除了可以委派对 Azure 存储对象的访问权限，还能指定在使用共享访问签名时只能使用 HTTPS 协议的一个选项。 这确保任何发送有 SAS 令牌的链接的人都使用正确的协议。

用于访问 Azure 文件共享的 [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) 支持加密，并且可以在 Windows Server 2012 R2、Windows 8、Windows 8.1 和 Windows 10 中使用，允许跨区域访问，甚至桌面上的访问。

客户端加密在将数据发送到 Azure 存储之前对其进行加密，所以在通过网络传输时数据是加密的。

### <a name="smb-encryption-over-azure-virtual-networks"></a>通过 Azure 虚拟网络的 SMB 加密 

使用在 Windows Server 2012 及更高版本中运行的 Azure VM 中的 [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server)，能够通过 Azure虚拟网络对传输中的数据进行加密，确保数据传输的安全，防止篡改和窃听攻击。 管理员可以为整个服务器启用 SMB 加密，也可以启用特定的共享。

默认情况下，为共享或服务器启用 SMB 加密后，只允许 SMB 的 3 个客户端访问加密共享。

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Azure 虚拟机中的传输中加密

根据连接的性质，通过多种方式对 Azure VM 之间传输的数据进行加密。

### <a name="rdp-sessions"></a>RDP 会话

可以使用 Windows 客户端计算机或者安装了 RDP 客户端的 Mac 上的[远程桌面协议](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) 连接并登录到 Azure VM。 在 RDP 会话中通过网络传输的数据可以受到 TLS 的保护。

还可使用远程桌面连接到 Azure 中的 Linux VM。

### <a name="secure-access-to-linux-vms-with-ssh"></a>使用 SSH 安全访问 Linux VM

可以使用[安全外壳](../virtual-machines/linux/ssh-from-windows.md) (SSH) 连接到在 Azure 中运行的 Linux VM 进行远程管理。 SSH 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 它是在 Azure 中托管的 Linux VM 的默认连接协议。 通过使用 SSH 密钥进行身份验证，无需使用密码进行登录。 SSH 使用公钥/私钥对（非对称加密）进行身份验证。

## <a name="azure-vpn-encryption"></a>Azure VPN 加密

连接到 Azure 时可以使用虚拟专用网络，它可以创建安全通道以保护通过网络发送的数据的隐私。

### <a name="azure-vpn-gateway"></a>Azure VPN 网关

[Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)可跨公共连接在虚拟网络和本地位置之间发送加密流量，或者在虚拟网络之间发送流量。

站点到站点 VPN 使用 [IPsec](https://en.wikipedia.org/wiki/IPsec) 传输加密。 Azure VPN网关使用一组默认提议。 可将 Azure VPN 网关配置为使用具有特定加密算法和关键优势的自定义 IPsec/IKE 策略，而不是 Azure 默认策略集。

### <a name="point-to-site-vpn"></a>点到站点 VPN

点到站点 VPN 允许单个客户端计算机访问 Azure 虚拟网络。 [安全套接字隧道协议](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) 用于创建 VPN 隧道，并可以穿过防火墙（隧道显示为 HTTPS 连接）。 可将自己的内部 PKI 根 CA 用于点到站点连接。

可以使用具有证书身份验证或 PowerShell 的 Azure 门户，将点到站点 VPN 连接配置到虚拟网络。

若要了解有关 Azure VNet 的点到站点 VPN 连接的更多信息，请参阅：[使用证书身份验证配置 VNet 的点到站点连接：Azure 门户](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)和

[使用证书身份验证配置 VNet 的点到站点连接：PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>站点到站点 VPN 

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。

可以使用 Azure门户、PowerShell 或 Azure命令行接口 (CLI)，将站点到站点 VPN 连接配置到虚拟网络。

阅读以下内容，了解更多信息：

[在 Azure 门户中创建站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[创建站点到站点连接](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[使用 CLI 创建具有站点到站点 VPN 连接的虚拟网络](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Azure Data Lake 中的传输中加密

此外，还会始终在 Data Lake Store 中对传输数据（也称移动数据）加密。 除了在存储到永久性介质之前对数据加密，还会始终通过 HTTPS 对传输数据进行保护。 HTTPS 是 Data Lake Store REST 接口唯一支持的协议。

若要了解有关 Azure Data Lake 中传输中数据加密的更多信息，请参阅标题为 [Azure Data Lake Store 中的数据加密](../data-lake-store/data-lake-store-encryption.md)的文档。

## <a name="key-management-with-azure-key-vault"></a>使用 Azure Key Vault 的密钥管理

如果没有适当地保护和管理密钥，加密会变得毫无用处。 Azure Key Vault 是 Microsoft 推荐的解决方案，用于管理和控制云服务使用的对加密密钥的访问。 访问密钥的权限可以通过 Azure Active Directory 帐户分配给服务或用户。

Azure Key Vault 可以帮助组织减少配置、修补以及维护硬件安全模块 (HSM) 和密钥管理软件的需求。 通过 Azure Key Vault，Microsoft 永远看不到你的密钥，应用程序无法直接访问它们；由你掌控。 也可以在 HSM 中导入或生成密钥。

## <a name="next-steps"></a>后续步骤

- [Azure 安全概述](security-get-started-overview.md)
- [Azure 网络安全概述](security-network-overview.md)
- [Azure 数据库安全性概述](azure-database-security-overview.md)
- [Azure 虚拟机安全概述](security-virtual-machines-overview.md)
- [静态数据加密](azure-security-encryption-atrest.md)
- [数据安全与加密最佳实践](azure-security-data-encryption-best-practices.md)

