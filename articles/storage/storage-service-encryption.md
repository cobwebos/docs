---
title: "静态数据的 Azure 存储服务加密 | Microsoft Docs"
description: "使用 Azure 存储空间服务加密功能可在存储数据时在服务端加密 Azure Blob 存储，并在检索数据时解密数据。"
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 680f41dc15b9681059847174a6910cfc937abd8b
ms.lasthandoff: 04/21/2017


---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>静态数据的 Azure 存储空间服务加密
静态数据的 Azure 存储空间服务加密 (SSE) 可帮助你保护数据，使你的组织能够信守在安全性与合规性方面所做的承诺。 使用此功能，Azure 存储空间可以先自动加密数据，再将数据保存到存储空间，并在检索之前解密数据。 加密、解密和密钥管理对于用户而言是完全透明的。

以下部分提供有关如何使用存储服务加密功能的详细指导，以及支持的方案和用户体验。

## <a name="overview"></a>概述
Azure 存储空间提供配套的安全性功能，这些功能相辅相成，可让开发人员共同构建安全的应用程序。 在应用程序和 Azure 之间传输数据时，可使用[客户端加密](storage-client-side-encryption.md)、HTTPS 或 SMB 3.0 保护数据。 存储服务加密提供静态加密，以完全透明的方式处理加密、解密和密钥管理。 采用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)所有数据，它是现在最强有力的分组密码之一。

SSE 的工作方式是在数据写入到 Azure 存储时对其加密，可用于 Azure Blob 存储和文件存储（预览版）。 它适用于：

* 一般用途的存储帐户和 Blob 存储帐户
* 标准存储和高级存储 
* 所有冗余级别（LRS、ZRS、GRS、RA-GRS）
* Azure Resource Manager 存储帐户（非经典） 
* 适用于 Blob 存储的所有区域。 有关文件存储，请查看“可用性”部分。

存储服务加密 - 文件预览版 SSE 现在可用于加密文件存储中的数据。 此功能目前处于预览状态。 下面给出的是提供适用于文件存储的 SSE 的区域列表。

若要参与 SSE 文件预览版，请联系 ssediscussions@microsoft.com。

若要了解详细信息，请参阅“常见问题解答”。

### <a name="availability-for-file-storage"></a>文件存储的可用性
目前，在所有 Azure 区域中提供适用于文件存储的存储服务加密。


若要启用或禁用存储帐户的存储服务加密，请登录 [Azure 门户](https://azure.portal.com)，然后选择存储帐户。 在“设置”边栏选项卡中，寻找如屏幕截图所示的“Blob 服务”部分，然后单击“加密”。

![显示加密选项的门户截图](./media/storage-service-encryption/image1.png)
<br/>*图 1：为 Blob 服务启用 SSE（步骤 1）*

![显示加密选项的门户截图](./media/storage-service-encryption/image3.png)
<br/>*图 2：为文件服务启用 SSE（步骤 1）*

单击“加密”设置后，可以启用或禁用存储服务加密。

![显示加密属性的门户截图](./media/storage-service-encryption/image2.png)
<br/>*图 1.1：为 Blob 服务启用 SSE（步骤 2）*

![显示加密属性的门户截图](./media/storage-service-encryption/image4.png)
<br/>*图 2.1：为文件服务启用 SSE（步骤 2）*
## <a name="encryption-scenarios"></a>加密方案
可以在存储帐户级别启用存储服务加密。 它支持以下客户方案：

* 加密 Blob 存储和文件存储。
* Blob 服务（而不是文件服务）的加密功能支持加密迁移到 Resource Manager 存储帐户的经典存储帐户。
* 只有新创建的存储帐户才支持文件存储加密。

SSE 具有以下限制：

* 不支持经典存储帐户的加密。
* Blob 服务（而不是文件服务）的加密功能支持加密迁移到 Resource Manager 存储帐户的经典存储帐户。
* 只有新创建的存储帐户才支持文件存储加密。
* 现有数据 - SSE 只将加密启用加密之后新建的数据。 例如，如果你创建新的 Resource Manager 存储帐户但未打开加密，然后将 blob 或存档 VHD 上传到该存储帐户，然后打开 SSE，则那些 Blob 不会被加密，除非重新写入或复制。
* 应用商店支持 - 使用 [Azure 门户](https://portal.azure.com)、PowerShell 和 Azure CLI 为应用商店中创建的 VM 启用加密。 VHD 基本映像将保持未加密状态；但是，在 VM 启动之后完成的任何写入将会加密。
* 表和队列数据将不会加密。

## <a name="getting-started"></a>入门
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>步骤 1：[创建新存储帐户](storage-create-storage-account.md)。
### <a name="step-2-enable-encryption"></a>步骤 2：启用加密。
可以使用 [Azure 门户](https://portal.azure.com)启用加密。

> [!NOTE]
> 如果想要以编程方式启用或禁用存储帐户上的存储服务加密，可以使用 [Azure 存储资源提供程序 REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)、[.NET 存储资源提供程序](https://msdn.microsoft.com/library/azure/mt131037.aspx)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure CLI](storage-azure-cli.md)。
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>步骤 3：将数据复制到存储帐户
如果为 Blob 服务启用 SSE，则写入到该存储帐户的任何 blob 会进行加密。 除非重新编写，否则不会加密已存在于该存储帐户中的任何 blob。 可以将数据从一个存储帐户复制到另一个使用 SSE 加密的存储帐户，甚至还可以启用 SSE 并将 blob 从一个容器复制到另一个容器，以确保以前的数据已加密。 可以使用以下任何工具来实现此目的。 对于文件存储，也可以采用相同行为。

#### <a name="using-azcopy"></a>使用 AzCopy
AzCopy 是一个 Windows 命令行实用程序，专用于使用具有优化性能的简单命令将数据复制到 Microsoft Azure Blob、文件和表存储以及从这些位置复制数据。 可使用此工具将 blob 或文件从一个存储帐户复制到另一个启用了 SSE 的存储帐户。 

有关详细信息，请参阅[使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)。

#### <a name="using-smb"></a>使用 SMB
Azure 文件存储使用标准 SMB 协议在云中提供文件共享。 可以从本地客户端或在 Azure 中装载文件共享。 装载后，可以使用 Robocopy 等工具将文件复制到 Azure 文件共享。 有关详细信息，请参阅[如何在 Windows 上装载 Azure 文件共享](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-files#mount-the-file-share)和[如何在 Linux 上装载 Azure 文件共享](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share)。


#### <a name="using-the-storage-client-libraries"></a>使用存储客户端库
可以使用我们丰富的存储客户端库集，包括 .NET、C++、Java、Android、Node.js、PHP、Python 和 Ruby，在 Blob 存储或存储帐户之间相互复制 blob 或文件数据。

有关详细信息，请访问[使用 .NET 的 Azure Blob 存储入门](storage-dotnet-how-to-use-blobs.md)。

#### <a name="using-a-storage-explorer"></a>使用存储空间资源管理器
可以使用存储资源管理器创建存储帐户、上传和下载数据、查看 Blob 内容，以及浏览目录。 可以使用其中一个存储空间资源管理器将 Blob 上载到已启用加密的存储帐户。 使用某些存储资源管理器，还可以将现有 Blob 存储中的数据复制到存储账户中的不同容器或已启用 SSE 的新存储帐户。

有关详细信息，请访问 [Azure 存储资源管理器](storage-explorers.md)。

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>步骤 4：查询加密数据的状态
已部署存储客户端库的更高版本，可让你查询对象的状态，从而判断其是否已加密。 此版本目前仅适用于 Blob 存储。 对文件存储的支持在计划之中。 

在此同时，可以调用[获取帐户属性](https://msdn.microsoft.com/library/azure/mt163553.aspx)来验证存储帐户是否已启用加密，或者在 Azure 门户中查看存储帐户属性。

## <a name="encryption-and-decryption-workflow"></a>加密和解密工作流
下面是加密/解密工作流的简要描述：

* 客户对存储帐户启用加密。
* 当客户将新数据（PUT Blob、PUT 块、PUT 页、PUT 文件等）写入 Blob 或文件存储时，每个写入将使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强块加密法之一）进行加密。
* 当客户需要访问数据（GET Blob 等）时，数据将在返回给用户之前自动解密。
* 如果已禁用加密，将不会再加密新的写入，现有加密数据在用户重新写入之前将保持加密。 启用加密时，向 Blob 或文件存储的写入将会加密。 数据状态在用户启用/禁用存储帐户的加密之间切换时不会更改。
* 所有加密密钥将由 Microsoft 存储、加密和管理。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>有关静态数据存储服务加密的常见问题
**问：我有一个现有的经典存储帐户。可以在其上启用 SSE 吗？**

答：不可以，只有 Resource Manager 存储帐户支持 SSE。

**问：如何在经典存储帐户中加密数据？**

答：可以创建新的 Resource Manager 存储帐户，并使用 [AzCopy](storage-use-azcopy.md) 将数据从现有经典存储帐户复制到新建的 Resource Manager 存储帐户。 

如果将经典存储帐户迁移到 Resource Manager 存储帐户，则在迁移过程中不会加密数据。 但是，如果迁移存储帐户后启用加密，则写入到该存储帐户的任何新数据将会加密。 有关详细信息，请参阅[Platform Supported Migration of IaaS Resources from Classic to Resource Manager](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)（平台支持的从经典部署模型到 Resource Manager 部署模型的 IaaS 资源迁移）。 请注意，仅 Blob 存储支持此功能。 对于文件存储预览版，用户必须创建新的 Resource Manager 存储帐户。

**问：我有一个现有的 Resource Manager 存储帐户。可以在其上启用 SSE 吗？**

答：是的，但只加密新写入的 Blob。 它不会返回去对已经存在的数据进行加密。 文件存储预览版尚不支持此功能。

**问：如何加密现有 Resource Manager 存储帐户中的当前数据？**

答：可以随时在 Resource Manager 存储帐户中启用 SSE。 但是，不会加密已经存在的 Blob。 若要加密这些 Blob，可以将它们复制到另一个名称或另一个容器，然后删除未加密的版本。文件存储预览版尚不支持此功能

**问：我使用高级存储，可以使用 SSE 吗？**

答：可以，标准存储和高级存储都支持 SSE。文件存储预览版尚不支持此功能。

**问：如果我创建新的存储帐户并启用 SSE，然后使用该存储帐户创建新的 VM，是否表示我的 VM 已加密？**

答：是的。 使用新存储帐户创建的任何磁盘将会加密，只要它们是在启用 SSE 之后创建的。 如果 VM 是使用 Azure 应用商店创建的，VHD 基本映像将保持未加密状态；但是，在 VM 启动之后完成的任何写入将会加密。

**问：是否可以使用 Azure PowerShell 和 Azure CLI 创建新的存储帐户并启用 SSE？**

答：是的。

**问：如果已启用 SSE，Azure 存储的费用将会高多少？**

答：没有任何额外费用。

**问：加密密钥由谁管理？**

答：密钥由 Microsoft 管理。

**问：我可以使用自己的加密密钥吗？**

答：我们正在努力提供相应的功能让客户使用他们自己的加密密钥。

**问：是否可以吊销加密密钥的访问权限？**

答：暂时不可以。密钥完全由 Microsoft 管理。

**问：创建新的存储帐户时是否会按默认启用 SSE？**

答：默认情况下不启用 SSE；可以使用 Azure 门户来启用它。 也可以编程方式使用存储资源提供程序 REST API 来启用此功能。

**问：此功能与 Azure 磁盘加密有何不同？**

答：此功能用于加密 Azure Blob 存储中的数据。 Azure 磁盘加密用于加密IaaS VM 中的 OS 和数据磁盘。 有关详细信息，请访问我们的[存储安全指南](storage-security-guide.md)。

**问：如果我启用 SSE，然后在磁盘上启用 Azure 磁盘加密，会发生什么情况？**

答：不会有任何问题。 这两种方法都会加密数据。

**问：我的存储帐户设置为异地冗余复制。如果启用 SSE，我的冗余副本是否也会加密？**

答：是的，将加密存储帐户的所有副本，并且支持所有冗余选项 – 本地冗余存储 (LRS)、区域冗余存储空间 (ZRS)、异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS)。

**问：我无法对我的存储帐户启用加密。**

答：它是 Resource Manager 存储帐户吗？ 不支持经典存储帐户。 

**问：是否只有特定区域允许 SSE？**

答：SSE 在适用于 Blob 存储的所有区域中提供。 有关文件存储，请查看“可用性”部分。 

**问：如果遇到问题或想要提供反馈时怎样进行联系？**

答：任何有关存储服务加密的问题，请联系 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。

## <a name="next-steps"></a>后续步骤
Azure 存储空间提供配套的安全性功能，这些功能相辅相成，可让开发人员共同构建安全的应用程序。 有关详细信息，请访问[存储安全指南](storage-security-guide.md)。


