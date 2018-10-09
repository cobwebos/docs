---
title: Azure 存储帐户概述 | Microsoft Docs
description: 了解用于创建和使用 Azure 存储帐户的选项。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/13/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 0b2bf8cdb1af85e5ddbd3b18dd6dfa47bcb835b4
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432879"
---
# <a name="azure-storage-account-overview"></a>Azure 存储帐户概述

Azure 存储帐户包含所有 Azure 存储数据对象：Blob、文件、队列、表和磁盘。 Azure 存储帐户中的数据持久保存、高度可用、安全、可大规模缩放，并且可以在任意位置通过 HTTP 或 HTTPS 来访问。 

若要了解如何创建 Azure 存储帐户，请参阅[创建存储帐户](storage-quickstart-create-account.md)。

## <a name="types-of-storage-accounts"></a>存储帐户的类型

Azure 存储提供三种类型的存储帐户。 每个类型支持不同的功能，并具有自身的定价模型。 在创建存储帐户之前，请考虑到这些差异，以确定最适合应用程序的帐户类型。 存储帐户的类型包括：

* **常规用途 v2** 帐户（建议用于大多数方案）
* **常规用途 v1** 帐户
* **Blob 存储**帐户

下表描述了存储帐户的类型及其功能：

| 存储帐户类型 | 支持的服务                       | 支持的性能层 | 支持的访问层               | 复制选项                                                | 部署模型<sup>1</sup>  | 加密<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| 常规用途 V2   | Blob、文件、队列、表和磁盘       | 标准、高级           | 热、冷、存档<sup>3</sup> | LRS、ZRS<sup>4</sup>、GRS、RA-GRS | 资源管理器 | 加密  |
| 常规用途 V1   | Blob、文件、队列、表和磁盘       | 标准、高级           | 不适用                                  | LRS、GRS、RA-GRS                                                   | 资源管理器、经典  | 加密  |
| Blob 存储         | Blob（仅限块 Blob 和追加 Blob） | 标准                    | 热、冷、存档<sup>3</sup>                            | LRS、GRS、RA-GRS                                                   | 资源管理器  | 加密  |

<sup>1</sup>建议使用 Azure 资源管理器部署模型。 仍将在某些位置创建使用经典部署模型的存储帐户，继续支持现有的经典帐户。 有关详细信息，请参阅 [Azure 资源管理器与经典部署：了解部署模型和资源状态](../../azure-resource-manager/resource-manager-deployment-model.md)。

<sup>2</sup>使用针对静态数据的存储服务加密 (SSE) 来加密所有存储帐户。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](storage-service-encryption.md)。

<sup>3</sup>存档层仅在单个 Blob 级别可用，在存储帐户级别不可用。 只能存档块 Blob 和追加 Blob。 有关详细信息，请参阅 [Azure Blob 存储：热、冷和存档存储层](../blobs/storage-blob-storage-tiers.md)。

<sup>4</sup>区域冗余存储 (ZRS) 仅适用于标准常规用途 v2 存储帐户。 有关 ZRS 的详细信息，请参阅[区域冗余存储 (ZRS)：具有高可用性的 Azure 存储应用程序](storage-redundancy-zrs.md)。 有关其他复制选项的详细信息，请参阅 [Azure 存储复制](storage-redundancy.md)。

### <a name="general-purpose-v2-accounts"></a>常规用途 v2 帐户

常规用途 v2 存储帐户支持最新的 Azure 存储功能，并整合了常规用途 v1 和 Blob 存储帐户的所有功能。 常规用途 v2 帐户为 Azure 存储提供最低的每 GB 容量价格，以及具有行业竞争力的事务处理价格。 常规用途 v2 存储帐户支持以下 Azure 存储服务：

- Blob（所有类型）
- 文件
- 磁盘
- 队列
- 表

Microsoft 建议对大多数方案使用常规用途 v2 存储帐户。 可以轻松将常规用途 v1 或 Blob 存储帐户升级到常规用途 v2 帐户，无需停机或重新编写应用程序，且无需复制数据。 有关升级到常规用途 v2 帐户的详细信息，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。 

常规用途 v2 存储帐户提供多个访问层用于根据使用模式存储数据。 有关详细信息，请参阅[块 Blob 数据的访问层](#access-tiers-for-block-blob-data)。

### <a name="general-purpose-v1-accounts"></a>常规用途 v1 帐户

常规用途 v1 帐户可以访问所有 Azure 存储服务，但可能没有最新功能，其每 GB 定价也可能不是最低的。 常规用途 v1 存储帐户支持以下 Azure 存储服务：

- Blob（所有类型）
- 文件
- 磁盘
- 队列
- 表

尽管建议在大多数情况下使用常规用途 v2 帐户，但对于以下方案，最适合使用常规用途 v1 帐户： 

* 应用程序需要 Azure 经典部署模型。 常规用途 v2 帐户和 Blob 存储帐户仅支持 Azure 资源管理器部署模型。

* 应用程序是事务密集型的或要占用大量的异地复制带宽，但不需要很大的容量。 在这种情况下，常规用途 v1 可能是最经济节省的选择。

* 使用了早于 2014-02-14 的 [存储服务 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 的版本或使用了版本低于 4.x 的客户端库，并且无法升级应用程序。

### <a name="blob-storage-accounts"></a>Blob 存储帐户

Blob 存储帐户是将非结构化对象数据作为块 Blob 存储的专用存储帐户。 Blob 存储帐户提供与常规用途 v2 存储帐户相同的持久性、可用性、可伸缩性和性能功能。 Blob 存储帐户支持存储块 Blob 和追加 Blob，但不支持存储页 Blob。

Blob 存储帐户提供多个访问层用于根据使用模式存储数据。 有关详细信息，请参阅[块 Blob 数据的访问层](#access-tiers-for-block-blob-data)。

## <a name="naming-storage-accounts"></a>为存储帐户命名

为存储帐户命名时，请记住以下规则：

- 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
- 存储帐户名称在 Azure 中必须是唯一的。 没有两个存储帐户可以有相同的名称。

## <a name="performance-tiers"></a>性能层

可为以下任一性能层配置常规用途存储帐户：

* 用于存储 Blob、文件、表、队列和 Azure 虚拟机磁盘的标准性能层。
* 仅用于存储 Azure 虚拟机磁盘的高级性能层。 有关高级存储的详细概述，请参阅 [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../virtual-machines/windows/premium-storage.md) 。

## <a name="access-tiers-for-block-blob-data"></a>块 Blob 数据的访问层

Azure 存储提供不同的选项用于根据使用模式访问块 Blob 数据。 Azure 存储中的每个访问层已针对特定的数据使用模式进行优化。 根据需要选择适当的访问权限层，能以最经济高效的方式存储块 Blob 数据。

可用的访问层包括：

* **热**访问层：已针对存储帐户中频繁访问的对象进行优化。 访问热层中的数据是最经济高效的，不过，存储开销要高一些。 默认情况下，新存储帐户在热层中创建。
* **冷**存储层：已针对不常访问且存储时间至少为 30 天的大量数据进行优化。 将数据存储在冷层中是较为经济高效的做法，但访问这些数据的开销可能比访问热层中的数据更高一些。
* **存档**层：仅适用于单个块 Blob。 存档层已针对可以容忍数小时的检索延迟且会保留在存档层至少 180 天的数据进行优化。 存档层是用于存储数据的最经济高效的选项，但访问这些数据的开销比访问热层或冷层中的数据要高一些。 

> [!NOTE]
> [高级访问层](../blobs/storage-blob-storage-tiers.md#premium-access-tier)在北欧、美国东部 2、美国中部和美国西部区域中以本地冗余存储 (LRS) 帐户的形式提供，目前为受限预览版。 若要了解如何注册预览版，请参阅 [Azure 高级 Blob 存储简介](http://aka.ms/premiumblob)。

如果数据的使用模式有所更改，则可以随时在这些访问层之间切换。 

> [!IMPORTANT]
> 更改现有存储帐户或 Blob 的访问层可能会产生额外的费用。

有关访问层的详细信息，请参阅 [Azure Blob 存储：高级（预览版）、热、冷和存档存储层](../blobs/storage-blob-storage-tiers.md)。

## <a name="replication"></a>复制

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

有关存储复制的详细信息，请参阅 [Azure 存储复制](storage-redundancy.md)。

## <a name="encryption"></a>加密

存储帐户中的所有数据在服务端加密。 有关加密的详细信息，请参阅[静态数据的 Azure 存储服务加密](storage-service-encryption.md)。

## <a name="storage-account-endpoints"></a>存储帐户终结点

存储帐户在 Azure 中为数据提供唯一的命名空间。 在 Azure 存储中存储的每个对象都具有一个包含唯一帐户名称的地址。 帐户名称与 Azure 存储服务终结点的组合构成了存储帐户的终结点。

例如，如果常规用途存储帐户名为 *mystorageaccount*，则该帐户的默认终结点为：

* Blob 存储： http://*mystorageaccount*.blob.core.windows.net
* 表存储： http://*mystorageaccount*.table.core.windows.net
* 队列存储： http://*mystorageaccount*.queue.core.windows.net
* Azure 文件： http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Blob 存储帐户仅公开 Blob 服务终结点。

用于访问存储帐户中某个对象的 URL 是通过将存储帐户中对象的位置附加到终结点而构造的。 例如，Blob 地址可能具有以下格式： http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

还可将存储帐户配置为对 Blob 使用自定义域。 有关详细信息，请参阅[为 Azure 存储帐户配置自定义域名](../blobs/storage-custom-domain-name.md)。  

## <a name="control-access-to-account-data"></a>控制对帐户数据的访问

默认情况下，只有你，即帐户所有者，才能使用帐户中的数据。 可以控制谁能访问你的数据，以及他们拥有哪些权限。

针对存储帐户发出的每个请求必须经过授权。 在服务级别，该请求必须包含有效的 *Authorization* 标头，该标头包含服务在执行请求之前验证该请求所需的全部信息。

可使用以下任一方法授予对存储帐户中数据的访问权限：

- **Azure Active Directory：** 使用 Azure Active Directory (Azure AD) 凭据来验证要访问 Blob 和队列数据（预览版）的用户、组或其他标识的身份。 如果某个标识的身份验证成功，则 Azure AD 会返回一个令牌，在对访问 Azure Blob 存储或队列存储的请求授权时可以使用该令牌。 有关详细信息，请参阅[使用 Azure Active Directory 对 Azure 存储访问进行身份验证（预览版）](storage-auth-aad.md)。
- **共享密钥授权：** 使用存储帐户访问密钥构造一个连接字符串，应用程序在运行时将使用该连接字符串来访问 Azure 存储。 连接字符串中的值用于构造要传递给 Azure 存储的 *Authorization* 标头。 有关详细信息，请参阅[配置 Azure 存储连接字符串](storage-configure-connection-string.md)。
- **共享访问签名：** 如果不使用 Azure AD 身份验证，可以使用共享访问签名来委托对存储帐户中资源的访问权限。 共享访问签名是一个令牌，用于在 URL 中封装对 Azure 存储访问请求进行授权时所需的全部信息。 可以在共享访问签名中指定存储资源、授予的权限，以及权限的有效时间间隔。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 与其他授权方式相比，使用 Azure AD 凭据对用户或应用程序进行身份验证可以提供优越的安全性和易用性。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。 
>
> Microsoft 建议尽量对 Azure 存储 Blob 和队列应用程序使用 Azure AD 身份验证。

## <a name="copying-data-into-a-storage-account"></a>将数据复制到存储帐户

Microsoft 提供了用于从本地存储设备或第三方云存储提供程序导入数据的实用工具和库。 使用的解决方案取决于要传输的数据量。 

从常规用途 v1 或 Blob 存储帐户升级到常规用途 v2 帐户时，会自动迁移数据。 Microsoft 建议使用这种路径来升级帐户。 但是，如果你决定将数据从常规用途 v1 帐户移到 Blob 存储帐户，则需要使用下面所述的工具和库手动迁移数据。 

### <a name="azcopy"></a>AzCopy

AzCopy 是一个 Windows 命令行实用工具，旨在实现高性能地将数据复制到 Azure 存储和从 Azure 存储中复制。 可以使用 AzCopy 将数据从现有的常规用途存储帐户复制到 Blob 存储帐户，或从本地存储设备上传数据。 有关详细信息，请参阅[使用 AzCopy 命令行实用工具传输数据](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="data-movement-library"></a>数据移动库

适用于 .NET 的 Azure 存储数据移动库基于为 AzCopy 提供技术支持的核心数据移动框架。 库旨在实现类似于 AzCopy 的高性能、可靠且简单的数据传输操作。 可以通过它以本机模式充分利用应用程序中 AzCopy 提供的功能，无需运行和监视 AzCopy 的外部实例。 有关详细信息，请参阅[适用于 .Net 的 Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 或客户端库

可以创建自定义应用程序以使用其中一个 Azure 客户端库或 Azure 存储服务 REST API 将数据迁移到 Blob 存储帐户。 Azure 存储对多种语言和平台（如 .NET、Java、C++、Node.JS、PHP、Ruby 和 Python）提供了内容丰富的客户端库。 客户端库提供高级功能，如重试逻辑、日志记录和并行上传。 也可以直接针对可以由发出 HTTP/HTTPS 请求的任何语言调用的 REST API 进行开发。

有关 Azure 存储 REST API 的详细信息，请参阅 [Azure 存储服务 REST API 参考](https://docs.microsoft.com/rest/api/storageservices/)。 

> [!IMPORTANT]
> 使用客户端加密进行加密的 Blob 会将与加密相关的元数据与 Blob 一起存储。 如果复制使用客户端加密来加密的 Blob，请确保复制操作保留 Blob 元数据，尤其是与加密相关的元数据。 如果复制不包含此加密元数据的 Blob，则不能再次检索 Blob 内容。 有关加密相关元数据的详细信息，请参阅 [Azure 存储客户端加密](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

### <a name="azure-importexport-service"></a>Azure 导入/导出服务

如果要将大量的数据导入存储帐户，请考虑 Azure 导入/导出服务。 使用导入/导出服务可将磁盘驱动器寄送到 Azure 数据中心，从而安全地将大量数据导出到 Azure Blob 存储和 Azure 文件。 

还可以使用导入/导出服务将数据从 Azure Blob 存储传输到磁盘驱动器，然后再寄送到本地站点。 可将单个或多个磁盘驱动器中的数据导入到 Azure Blob 存储或 Azure 文件。 有关详细信息，请转到[什么是 Azure 导入/导出服务？](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。

## <a name="storage-account-billing"></a>存储帐户计费

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 Azure 存储帐户，请参阅[创建存储帐户](storage-quickstart-create-account.md)。
* 若要管理或删除现有存储帐户，请参阅[管理 Azure 存储帐户](storage-account-manage.md)。
