---
title: 使用 Azure 存储保护 PaaS 应用程序 | Microsoft Docs
description: " 了解有关保护 PaaS Web 和移动应用程序的 Azure 存储安全最佳做法。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 9d4251e61b60d8da6ce5072ba66aeaedb60cb33a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>使用 Azure 存储保护 PaaS Web 和移动应用程序
本文介绍有关保护 PaaS Web 和移动应用程序的 Azure 存储安全最佳做法。 这些最佳实践衍生自我们的 Azure 经验和客户经验。

[Azure 存储安全指南](../storage/common/storage-security-guide.md)是有关 Azure存储和安全性的详细信息的重要来源。  本文在较高的层次上介绍了安全指南中的一些概念，以及获得详细信息的安全指南和其他来源的链接。

## <a name="azure-storage"></a>Azure 存储
Azure 可以用本地不易实现的方式来部署并使用存储。 通过 Azure 存储，可用相对较少的工作量达到高水平的可伸缩性和可用性。 Azure 存储不仅是 Windows 和 Linux Azure 虚拟机的基础，还可以支持大型分布式应用程序。

Azure 存储提供以下四种服务：Blob 存储、表存储、队列存储和文件存储。 若要了解详细信息，请参阅 [Microsoft Azure 存储简介](../storage/storage-introduction.md)。

## <a name="best-practices"></a>最佳实践
本文将探讨以下最佳做法：

- 访问保护：
   - 共享访问签名 (SAS)
   - 托管磁盘
   - 基于角色的访问控制 (RBAC)

- 存储加密：
   - 高价值数据的客户端加密
   - 适用于虚拟机 (VM) 的 Azure 磁盘加密
   - 存储服务加密

## <a name="access-protection"></a>访问保护
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>使用共享访问签名而不是存储帐户密钥

在 IaaS 解决方案中，通常运行 Windows Server 或 Linux 虚拟机，使用访问控制机制保护文件免受泄露和篡改威胁。 在 Windows 上可以使用[访问控制列表 (ACL)](../virtual-network/virtual-networks-acl.md)，在 Linux 上可能会使用 [chmod](https://en.wikipedia.org/wiki/Chmod)。 从本质上讲，如果现在要保护自己数据中心的服务器上的文件，就需要执行这些操作。

PaaS 则不同。 在 Microsoft Azure 中存储文件的最常见方式之一是使用 [Azure Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 Blob 存储和其他文件存储之间的区别是文件 I/O 以及文件 I/O 附带的保护方法。

访问控制是关键。 若要帮助控制对 Azure 存储的访问，当[创建存储帐户](../storage/common/storage-create-storage-account.md)时，系统将生成两个 512 位存储帐户密钥 (SAK)。 在例程秘钥轮换期间，通过密钥冗余级别可避免服务中断。

存储访问密钥是高优先级的机密，只能由负责存储访问控制的人员访问。 如果不当人员可以访问这些密钥，他们能完全控制存储，并可以替换、删除文件或将文件添加到存储。 这些文件包括恶意内容和可能会危及组织或客户的其他类型的内容。

但你仍然需要一种方法来提供存储中对象的访问权限。 若要提供更精细的访问权限，可以利用[共享访问签名](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS)。 通过 SAS，可以使用特定权限在预定义的时间间隔共享存储中的特定对象。 通过共享访问签名，可定义：

- SAS 有效期限，包括开始时间和到期时间。
- SAS 授予的权限。 例如，Blob 中的 SAS 可能授予用户对 Blob 的读取和写入权限，但不授予删除权限。
- Azure 存储接受 SAS 的可选 IP 地址或 IP 地址范围。 例如，可以指定属于组织的 IP 地址范围。 这提供了针对 SAS 的另一个安全度量值。
- Azure 存储接受 SAS 所依据的协议。 此可选参数可用于限制使用 HTTPS 对客户端的访问。

通过 SAS 可以用想用的方式共享内容，而无需分配存储帐户密钥。 在应用程序中始终使用 SAS 可以安全地共享存储资源，不会危及存储帐户密钥。

若要了解详细信息，请参阅[使用共享访问签名](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS)。 有关潜在风险和降低风险的建议的详细信息，请参阅[使用 SAS 的最佳做法 ](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

### <a name="use-managed-disks-for-vms"></a>使用 VM 的托管磁盘

选择 [Azure 托管磁盘](../storage/storage-managed-disks-overview.md)时，Azure 将管理 VM 磁盘使用的存储帐户。 只需选择磁盘类型（高级或标准）和磁盘大小；Azure 存储将完成其余工作。 不必担心可能对多个存储帐户有其他要求的可伸缩性限制。

若要了解详细信息，请参阅[有关托管和非托管高级磁盘的常见问题解答](../storage/storage-faq-for-disks.md)。

### <a name="use-role-based-access-control"></a>使用基于角色的访问控制

之前讨论了使用共享访问签名 (SAS) 向其他客户端授予你的存储帐户中对象的访问权限，且无需公开帐户存储帐户密钥。 有时候，与针对存储帐户的特定操作相关联的风险要超过 SAS 所带来的好处。 有时候以其他方式管理访问会更简单。

管理访问的另一种方法是使用 [Azure 基于角色的访问控制](../role-based-access-control/overview.md) (RBAC)。 使用 RBAC 时，可根据了解内容的需要和最低特权的安全原则，专注于为员工提供所需的准确权限。 权限过多，可能会向攻击者公开帐户。 权限太少，员工无法有效完成其工作。 RBAC 通过对 Azure 提供细致的访问管理帮助解决此问题。 对于想要实施数据访问安全策略的组织，这是必须要做的事。

可以利用 Azure 中内置的 RBAC 角色向用户分配权限。 考虑将存储帐户参与者用于需要管理存储帐户的云操作员，并使用经典存储帐户参与者角色来管理经典存储帐户。 如果云操作员需要管理 VM 但不管理他们连接到的虚拟网络或存储帐户，请考虑将他们添加到虚拟机参与者角色。

未使用 RBAC 等功能实施数据访问控制的组织可能会给其用户分配超过需要的权限。 一开始就允许某些用户访问他们不应有权访问的数据可能会导致数据泄漏。

若要了解有关 RBAC 的详细信息，请参阅：

- [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)
- [用于 Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md)
- [Azure 存储安全指南](../storage/common/storage-security-guide.md)，详细介绍了如何使用 RBAC 保护存储帐户

## <a name="storage-encryption"></a>存储加密
### <a name="use-client-side-encryption-for-high-value-data"></a>对高价值数据使用客户端加密

通过客户端加密，可在上传到 Azure 存储之前以编程方式加密传输中的数据，并从存储中检索数据时以编程方式解密数据。  这提供传输中的数据加密，但也提供静态数据加密。  客户端加密是最安全的加密数据方法，但它要求以编程方式更改应用程序，并将密钥管理程序放在正确的位置。

客户端加密还可以对加密密钥进行单独控制。  可生成和管理自己的加密密钥。  客户端加密使用信封技术，其中 Azure 存储客户端库生成内容加密密钥 (CEK)，然后使用密钥加密密钥 (KEK) 包装（加密）密钥。 KEK 由密钥标识符标识，可以是非对称密钥对或对称密钥，还可以在本地托管或存储在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中。

客户端加密内置于 Java 和 .NET 存储客户端库中。  有关在客户端应用程序中加密数据并生成和管理自己的加密密钥的信息，请参阅[适用于 Microsoft Azure 存储的客户端加密和 Azure Key Vault](../storage/storage-client-side-encryption.md)。

### <a name="azure-disk-encryption-for-vms"></a>适用于 VM 的 Azure 磁盘加密
Azure 磁盘加密是用于帮助加密 Windows 和 Linux IaaS 虚拟机磁盘的功能。 Azure 磁盘加密利用 Windows 的行业标准 BitLocker 功能和 Linux 的 DM-Crypt 功能，为 OS 和数据磁盘提供卷加密。 该解决方案与 Azure Key Vault 集成，帮助用户管理 Key Vault 订阅中的磁盘加密密钥和机密。 此解决方案还可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。

请参阅[适用于 Windows 和 Linux IaaS VM 的 Azure 磁盘加密](azure-security-disk-encryption.md)。

### <a name="storage-service-encryption"></a>存储服务加密
当启用文件存储的[存储服务加密](../storage/storage-service-encryption.md)时，将使用 AES-256 加密自动加密数据。 Microsoft 处理所有加密、解密和密钥管理。 此功能适用于 LRS 和 GRS 冗余类型。

## <a name="next-steps"></a>后续步骤
本文介绍了有关保护 PaaS Web 和移动应用程序的一系列 Azure 存储安全最佳做法。 若要了解有关保护 PaaS 部署的详细信息，请参阅：

- [保护 PaaS 部署](security-paas-deployments.md)
- [使用 Azure 应用服务保护 PaaS Web 和移动应用程序](security-paas-applications-using-app-services.md)
- [在 Azure 中保护 PaaS 数据库](security-paas-applications-using-sql.md)
