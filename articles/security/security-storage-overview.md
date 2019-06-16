---
title: 可与 Azure 存储配合使用的安全功能 | Microsoft Docs
description: 本文概述可与 Azure 存储配合使用的核心 Azure 安全功能。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 74c24e05d974c4b3bb15a242185b645231f74580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792787"
---
# <a name="azure-storage-security-overview"></a>Azure 存储安全概述

本文概述可与 Azure 存储配合使用的 Azure 安全功能。 Azure 存储是依赖于持续性、可用性和伸缩性来满足客户需求的现代应用程序的云存储解决方案。 Azure 存储提供全面的安全功能。 可以：

* 使用基于角色的访问控制 (RBAC) 和 Azure Active Directory 对存储帐户进行安全保护。
* 使用客户端加密、HTTPS 或 SMB 3.0 对应用程序和 Azure 之间传输的数据进行安全保护。
* 可将数据设置为在写入 Azure 存储时使用存储服务加密自动进行加密。
* 将虚拟机 (VM) 使用的 OS 和数据磁盘设置为使用 Azure 磁盘加密进行加密。
* 使用共享访问签名 (SAS) 授予对 Azure 存储中数据对象的委派访问权限。
* 使用分析来跟踪某人访问存储时使用的身份验证方法。

有关 Azure 存储中安全性的详细信息，请参阅 [Azure 存储安全指南](../storage/common/storage-security-guide.md)。 本指南深入介绍了 Azure 存储的安全功能。 这些功能包括存储帐户密钥、传输中和静态中的数据加密以及存储分析。

## <a name="role-based-access-control"></a>基于角色的访问控制

可使用基于角色的访问控制来帮助保护存储帐户。 对于想要实施数据访问安全策略的组织而言，必须根据[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全策略限制访问权限。 这些访问权限是通过将相应的 RBAC 角色分配给特定范围内的组和应用程序来授予的。 可以使用[内置 RBAC 角色](../role-based-access-control/built-in-roles.md)（例如存储帐户参与者）将权限分配给用户。

了解更多：

* [Azure Active Directory 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>存储对象的委托访问权限

共享访问签名对存储帐户中的资源提供委托访问。 使用 SAS，意味着可以授权客户端在指定时间段内，以一组指定权限有限访问存储帐户中的对象。 可以授予这些有限的权限，而不必共享帐户访问密钥。

SAS 是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 要使用 SAS 访问存储资源，客户端只需将 SAS 提供给相应的构造函数或方法。

了解更多：

* [了解 SAS 模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [创建 SAS 并将其用于 Blob 存储](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

## <a name="encryption-in-transit"></a>传输中加密

传输中加密是通过网络传输数据时保护数据的一种机制。 在 Azure 存储中，可使用以下功能保护数据：

* [传输级别加密](../storage/common/storage-security-guide.md#encryption-in-transit)，例如将数据传入或传出 Azure 存储时使用的 HTTPS。
* [线路加密](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)，例如 Azure 文件共享的 SMB 3.0 加密。
* [客户端加密](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)，在将数据传输到存储之前加密数据，以及从存储传出数据后解密数据。

了解有关客户端加密的详细信息：

* [适用于 Microsoft Azure 存储的客户端加密](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [云安全控制系列：加密传输中的数据](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>静态加密

对许多组织而言，[静态数据加密](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是实现数据隐私性、符合性和数据所有权的必要措施。 可通过三种 Azure 功能进行静态数据加密：

* [存储服务加密](../storage/common/storage-security-guide.md#encryption-at-rest)始终处于启用状态，并在数据写入 Azure 存储时自动加密数据。
* [客户端加密](../storage/common/storage-security-guide.md#client-side-encryption)也提供静态加密功能。
* [Azure 磁盘加密](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines)允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。

了解有关存储服务加密的详细信息：

* [Azure 存储服务加密](https://azure.microsoft.com/services/storage/)适用于 [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)。 有关其他 Azure 存储类型的详细信息，请参阅 [Azure 文件存储](https://azure.microsoft.com/services/storage/files/)、[表存储](https://azure.microsoft.com/services/storage/tables/)、[队列存储](https://azure.microsoft.com/services/storage/queues/)。
* [静态数据的 Azure 存储服务加密](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure 磁盘加密

适用于虚拟机的 Azure 磁盘加密有助于解决组织安全性和符合性要求。 它使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 中控制的密钥和策略来加密 VM 磁盘（包括启动盘和数据磁盘）。

适用于 VM 的磁盘加密可用于 Linux 与 Windows 操作系统。 它也使用密钥保管库帮助你保护、管理和审核磁盘加密密钥的使用。 在 Azure 存储帐户中使用行业标准加密技术，对 VM 磁盘中的所有数据进行静态加密。 适用于 Windows 的磁盘加密解决方案是基于 [Microsoft BitLocker 驱动器加密](https://technet.microsoft.com/library/cc732774.aspx)技术，Linux 解决方案基于 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)。

了解详细信息

* [适用于 Windows 和 Linux IaaS 虚拟机的 Azure 磁盘加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>防火墙和虚拟网络

Azure 存储允许你为存储帐户启用防火墙规则。 启用后，它们将阻止传入的数据请求，包括来自其他 Azure 服务的请求。 可以配置例外以允许流量。 可以在现有存储帐户上或在创建时启用防火墙规则。

应该使用此功能将存储帐户保护到一组特定的允许网络。

有关 Azure 存储防火墙和虚拟网络的详细信息，请查看文章[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)

## <a name="azure-data-box"></a>Azure Data Box

Data Box、Data Box Disk 和 Data Box Heavy 设备可在网络不可用时将大量数据传输到 Azure。 这些脱机数据传输设备在组织和 Azure 数据中心之间往返运输。 它们使用 AES 加密来帮助保护传输中的数据，还在上传后执行一个清理过程，从设备中删除你的数据。

Data Box Edge 和 Data Box Gateway 是联机数据传输产品，它们用作网络存储网关来管理站点和 Azure 之间的数据。 Data Box Edge 是一种本地网络设备，可将数据传入和传出 Azure，并使用支持人工智能 (AI) 的边缘计算来处理数据。 Data Box Gateway 是具有存储网关功能的虚拟设备。

了解更多：

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>高级威胁防护

Azure 存储提供了高级威胁防护来实现额外的一层安全智能，用于检测试图访问或利用你的存储帐户的异常或可能有害的企图。 高级威胁防护监视 Azure 存储诊断日志来获取针对 Blob 存储的可疑读取、写入或删除请求。

可以从 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看高级威胁防护警报。 Azure 安全中心会提供有关检测到的任何可疑活动的详细信息，并提供用于针对潜在威胁进行调查和补救的建议操作。

了解更多：

* [Azure 存储高级威胁防护概述](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure 密钥保管库

Azure Disk Encryption 使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 来帮助控制和管理 Key Vault 订阅中的磁盘加密密钥和机密。 它还可确保虚拟机磁盘上的所有数据在 Azure 存储中静态加密。 应使用密钥保管库来审核密钥和策略的使用。

了解详细信息

* [什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)
