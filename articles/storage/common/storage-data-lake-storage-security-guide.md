---
title: Azure Data Lake Storage Gen2 存储安全指南 | Microsoft Docs
description: 详细介绍保护 Azure 存储的多种方法，包括但不限于 RBAC 和存储服务加密
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: ce01301455c7abcd26006e622fcfbb8127e1c511
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372482"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Azure Data Lake Storage Gen2 安全指南

Azure Data Lake Storage Gen2 是基于 Azure 存储帐户构建的一组功能。 因此，本文提到的所有内容均适用于启用了分层命名空间的 Azure 存储帐户（Data Lake Storage Gen2 功能）。

- 所有写入 Azure 存储的数据，使用[存储服务加密 (SSE)](storage-service-encryption.md) 进行自动加密。 有关详细信息，请参阅[宣布推出针对 Azure Blob、文件、表和队列存储的默认加密](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。
- Azure 存储支持使用 Azure Active Directory (Azure AD) 和基于角色的访问控制 (RBAC) 进行资源管理操作和数据操作，如下所示：
    - 可以将作用域为存储帐户的 RBAC 角色分配给安全主体，并使用 Azure AD 为密钥管理之类的资源管理操作授权。
    - 支持通过 Azure AD 集成在 Azure 存储上执行数据操作。 可以将范围为订阅、资源组、存储帐户或单个文件系统的 RBAC 角色分配给 Azure 资源的某个安全主体或托管标识。 有关详细信息，请参阅[使用 Azure Active Directory 对 Azure 存储访问进行身份验证](storage-auth-aad.md)。
- 在 Azure 存储中，可以使用[共享访问签名](../storage-dotnet-shared-access-signature-part-1.md)授予数据对象的委派访问权限。

本文概述其中每项可配合 Azure 存储使用的安全功能。 提供的文章链接提供每项功能的详细信息，让你轻松地进一步探讨每个主题。

下面是本文涵盖的主题：

* [管理平面安全性](#management-plane-security) - 保护存储帐户

  管理平面包含用于管理存储帐户的资源。 此部分包括 Azure 资源管理器部署模型，以及如何使用基于角色的访问控制 (RBAC) 来控制对存储帐户的访问。 还解决了如何管理存储帐户密钥以及重新生成此类密钥。
* [数据平面安全性](#data-plane-security) - 保护对数据的访问

  此部分探讨如何在存储帐户（例如文件和目录）中，允许使用共享访问签名和存储访问策略来访问实际的数据对象。 我们介绍服务级别 SAS 和帐户级别 SAS。 此外，介绍如何限制访问特定的 IP 地址（或 IP 地址范围）、如何限制用于 HTTPS 的协议，以及如何吊销共享访问签名而无需等到它过期。
* [传输中加密](#encryption-in-transit)

此部分讨论如何在将数据传输到启用了 Data Lake Storage Gen2 的存储帐户或从中传出时提供保护。 我们将探讨推荐的 HTTPS 用法。

## <a name="management-plane-security"></a>管理平面安全性

管理平面包含影响存储帐户本身的操作。 例如，可以创建或删除存储帐户、获取订阅中的存储帐户列表、检索存储帐户密钥，或重新生成存储帐户密钥。

本指南重点介绍部署的资源管理器模型，这种模型用于创建具有 Data Lake Storage Gen2 功能的存储帐户。 使用 Resource Manager 存储帐户而不是提供整个订阅的访问权限，可以使用基于角色的访问控制 (RBAC) 以更高的限制级别来控制对管理平面的访问。

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>如何使用基于角色的访问控制 (RBAC) 来保护存储帐户

让我们讨论 RBAC 是什么及其用法。 每个 Azure 订阅都有一个 Azure Active Directory。 可以为来自该目录的用户、组和应用程序授予访问权限，以便在使用 Resource Manager 部署模型的 Azure 订阅中管理资源。 此类型的安全性称为基于角色的访问控制 (RBAC)。 要管理此访问权限，请使用 Azure 门户中的“访问控制 (IAM)”。

通过 Resource Manager 模型，可将存储帐户置于资源组中，并使用 Azure Active Directory 控制对该特定存储帐户的管理平面的访问。 例如，可以授权特定用户访问存储帐户密钥，而其他用户可以查看有关存储帐户的信息，但无法访问存储帐户密钥。

#### <a name="granting-access"></a>授予访问权限

访问权限是通过将相应的 RBAC 角色分配给适当范围的用户、组和应用程序来授予的。 若要授予对整个订阅的访问权限，请在订阅级别分配角色。 可以将权限授予资源组本身，以此授予资源组中所有资源的访问权限。 也可以将特定角色分配给特定资源，例如存储帐户。

下面是需要知道的有关使用 RBAC 访问 Azure 存储帐户的管理操作的要点：

* 针对有权访问存储帐户中数据对象的用户，可为他们提供读取存储帐户密钥的权限，用户便可以使用这些密钥来访问文件和目录。
* 可将角色以分配给特定的用户帐户、用户组或应用程序。
* 每个角色都有“操作”和“非操作”列表。 例如，“虚拟机参与者”角色具有“listKeys”操作，允许读取存储帐户密钥。 “参与者”具有“非操作”，例如在 Active Directory 中更新用户的访问权限。
* 存储的角色包括（但不限于）以下角色：

  * 所有者 – 他们可以管理一切，包括访问权限。
  * 参与者 – 他们可以执行所有者可执行的所有操作，但分配访问权限除外。 拥有此角色的用户可以查看和重新生成存储帐户密钥。 他们可以使用存储帐户密钥来访问数据对象。
  * 读者 – 他们可以查看有关存储帐户的信息（机密除外）。 例如，如果将存储帐户中拥有读取者权限的角色分配给某个用户，该用户就可以查看存储帐户的属性，但无法对属性进行任何更改或查看存储帐户密钥。
  * 存储帐户参与者 - 他们可以管理存储帐户 - 他们可以读取订阅的资源组和资源，以及创建和管理订阅资源组部署。 他们也可以访问存储帐户密钥，这又意味着他们可以访问数据平面。
  * 用户访问管理员 – 他们可以管理对存储帐户的用户访问权限。 例如，他们可将“读取者”权限授予特定的用户。
  * 虚拟机参与者 – 他们可以管理虚拟机，但无法管理他们连接的存储帐户。 此角色可以列出存储帐户密钥，意味着分配此角色的用户可以更新数据平面。

    为了让用户能够创建虚拟机，他们必须能够在存储帐户中创建相应的 VHD 文件。 为此，他们需要能够检索存储帐户密钥，并将它传递给创建 VM 的 API。 因此，他们必须拥有此权限才能列出存储帐户密钥。
* 定义自定义角色的功能可让你从 Azure 资源上执行的可用操作列表中编写一系列操作。
* 必须先在 Azure Active Directory 中设置用户，才能为他们分配角色。
* 可以创建一份报告，其中描述了哪个用户已使用 PowerShell 或 Azure CLI 在哪个范围为哪些对象授予/吊销哪种类型的访问权限。

#### <a name="resources"></a>资源

* [Azure Active Directory 基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)

  此文解释了 Azure Active Directory 基于角色的访问控制及其工作原理。
* [RBAC：内置角色](../../role-based-access-control/built-in-roles.md)

  此文详细说明了 RBAC 中所有可用的内置角色。
* [了解 Resource Manager 部署和经典部署](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>管理存储帐户密钥

存储帐户密钥是由 Azure 创建的 512 位字符串，配合存储帐户名称用于访问存储于存储帐户中的数据对象，例如，Blob、表中的实体、队列消息，以及 Azure 文件共享中的文件。 控制对存储帐户密钥的访问就能控制对该存储帐户的数据平面的访问。

每个存储帐户在 [Azure 门户](https://portal.azure.com/)和 PowerShell cmdlet 中有两个密钥，分别称为“密钥 1”和“密钥 2”。 可采用多种多种方式手动重新生成这些密钥，包括（但不限于）使用 [Azure 门户](https://portal.azure.com/)、PowerShell、Azure CLI，或以编程方式使用 .NET 存储客户端库或 Azure 存储服务 REST API。

有许多原因会导致重新生成存储帐户密钥。

* 可出于安全原因而定期重新生成密钥。
* 如果某人设法侵入应用程序并检索硬编码或存储在配置文件中的密钥来为他们提供存储帐户的完整访问权限，则必须重新生成存储帐户密钥。
* 如果团队使用存储资源管理器应用程序来保留存储帐户密钥，而其中一位团队成员离职，则也需要重新生成密钥。 在某人离职后，应用程序仍将继续运行，使其他成员可以访问存储帐户。 这实际上是他们创建帐户级别共享访问签名的主要原因 – 可以改用帐户级别的 SAS，而不是将访问密钥存储在配置文件中。

#### <a name="key-regeneration-plan"></a>密钥重新生成计划

你不希望在不进行某些规划的情况下单纯重新生成你使用的密钥。 如果这样做，可能将切断对该存储帐户的所有访问权限，而这会造成严重中断。 这就是为什么要有两个密钥。 一次应该重新生成一个密钥。

重新生成密钥之前，先确保拥有依赖于存储帐户的所有应用程序列表，以及在 Azure 中使用的所有其他服务。 例如，如果 Azure 媒体服务依赖于存储帐户，则必须在重新生成密钥后将访问密钥与媒体服务重新同步。 如果使用存储资源管理器之类的任何应用程序，也必须为这些应用程序提供新的密钥。 如果 VM 的 VHD 文件存储在存储帐户中，它们将不会受到重新生成存储帐户密钥的影响。

可以在 Azure 门户中重新生成密钥。 重新生成密钥之后，最多 10 分钟后即可跨存储服务进行同步。

准备就绪后，请遵循以下常规过程，详细了解应如何更改密钥。 在本例中，假设你当前使用的是密钥 1，并想要更改所有项目以改用密钥 2。

1. 重新生成密钥 2 以确保密钥的安全。 可在 Azure 门户中执行此操作。
2. 在存储密钥所存储到的所有应用程序中，更改存储密钥以使用密钥 2 的新值。 测试并发布应用程序。
3. 在所有应用程序和服务成功启动并运行之后，重新生成密钥 1。 这可确保未明确提供新密钥的任何人都不再拥有存储帐户的访问权限。

如果当前使用密钥 2，可以使用相同的过程，但反转密钥名称。

可以过几天后迁移，更改每个应用程序来使用新的密钥并进行发布。 全部完成之后，应该返回并重新生成旧密钥，使其不再可用。

还可将存储帐户密钥作为机密放在 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)中，并让应用程序从此处检索该密钥。 然后，当重新生成密钥并更新 Azure 密钥保管库时，就不需要重新部署应用程序，因为它们将自动从 Azure 密钥保管库中选择新密钥。 可以让应用程序每次在需要密钥时读取它，或者，可以将它缓存在内存中，如果使用密钥时失败，将再次从 Azure Key Vault 中检索该密钥。

使用 Azure 密钥保管库还可以提高存储密钥的安全级别。 如果使用此方法，永远都不需要将存储密钥硬编码于配置文件中，这样将删除某人不需特定权限即可访问密钥的途径。

使用 Azure 密钥保管库的另一个优点是，还可使用 Azure Active Directory 来控制对密钥的访问。 这意味着，可以将访问权限授予少数必须从 Azure 密钥保管库检索密钥的应用程序，并了解其他应用程序在未特别授予它们权限的情况下无法访问密钥。

> [!NOTE]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1 并在其他地方使用密钥 2，将无法在没有部分应用程序失去访问的情况下轮转密钥。

#### <a name="resources"></a>资源

* [在 Azure 门户中管理存储帐户设置](storage-account-manage.md)
* [Azure Storage Resource Provider REST API Reference](https://msdn.microsoft.com/library/mt163683.aspx)（Azure 存储资源提供程序 REST API 参考）

## <a name="data-plane-security"></a>数据平面安全性
数据平面安全是指用于保护存储在 Azure 存储中的数据对象的方法。 我们已了解在传输数据期间加密数据和安全的方法，但该从何处着手来控制访问对象？

Azure 存储中数据对象的访问授权有三个选项，包括：

- 使用 Azure AD 授权对文件系统和队列的访问。 进行身份验证时，Azure AD 相对于其他方法具有很多优势，包括不需要将机密存储在代码中。 有关详细信息，请参阅[使用 Azure Active Directory 对 Azure 存储访问进行身份验证](storage-auth-aad.md)。 
- 使用存储帐户密钥通过共享密钥进行访问授权。 通过共享密钥进行授权需要将存储帐户密钥存储在应用程序中，因此 Microsoft 建议尽可能改用 Azure AD。
- 使用共享访问签名授予特定时间段对特定数据对象的受控权限。

除通过授权限制访问外，还可使用[防火墙和虚拟网络](storage-network-security.md)来根据网络规则限制对存储帐户的访问。  通过此方法，可拒绝对公共 Internet 流量的访问，并仅向特定 Azure 虚拟网络或公共 Internet IP 地址范围授予访问权限。

### <a name="storage-account-keys"></a>存储帐户密钥

存储帐户密钥是由 Azure 创建的 512 位字符串，配合存储帐户名称用于访问存储于存储帐户中的数据对象。

例如，你可以读取文件。 可通过 Azure 门户或利用诸多 存储资源管理器应用程序中的某一种执行其中的很多操作。 还可编写代码，利用 REST API 执行这些操作。

正如关于[管理平面安全性](#management-plane-security)一节所述，可通过基于角色的访问控制 (RBAC)，控制借助 Azure 资源管理器模型访问存储帐户存储密钥的情况。

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>如何使用共享访问签名和存储访问策略来委派对帐户中对象的访问权限

共享访问签名是一个字符串，包含可附加到 URI 的安全令牌，可让你委派存储对象的访问权限，以及指定访问的权限和日期/时间范围等限制。

你可以授予对文件或目录的访问权限。

可以为某位客户提供 SAS 令牌，使他们能够将图片上传到 Blob 存储中的文件系统，并为 Web 应用程序提供读取这些图片的权限。 在这两种情况下，都可实现关注点分离 – 每个应用程序只能获取执行其任务所需的访问权限。 这是通过使用共享访问签名来实现的。

#### <a name="why-you-want-to-use-shared-access-signatures"></a>使用共享访问签名的原因

为什么要使用 SAS 而不只是分发存储帐户密钥，哪一种方法更方便？ 分发存储帐户密钥就像是在存储王国中共享密钥。 它会授予完全访问权限。 其他人可以使用密钥，并将其整个音乐库上传到你的存储帐户。 他们也许还能将文件替换为受病毒感染的版本，或窃取数据。 不应草率地无限制分发对存储帐户的访问权限。

如果使用共享访问签名，可以只为客户端提供有限时间内所需的权限。 例如，如果有人将文件上传到你的帐户，则你可以授予他们足够时间的写入权限供其上传文件（当然，这取决于文件的大小）。 如果改变想法，可以吊销该访问权限。

此外，可以指定将使用 SAS 所发出的请求限制为特定的 IP 地址或 Azure 外部的 IP 地址范围。 你还可以要求使用特定协议（HTTPS 或 HTTP/HTTPS）来发出请求。 这意味着，如果只想要允许 HTTPS 流量，可以将所需的协议设置为仅限 HTTPS，并阻止 HTTP 流量。

#### <a name="definition-of-a-shared-access-signature"></a>共享访问签名的定义

共享访问签名是一组附加到指向资源的 URL 的查询参数

其中提供有关允许的访问权限的信息，以及准许该访问权限的时间长度。 下面提供了一个示例：此 URI 将提供对 Blob 的读取权限，期限为五分钟。 SAS 查询参数必须以 URL 编码，例如：%3A 表示冒号 (:)，%20 表示空格。

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Azure 存储服务对共享访问签名进行授权的方式

当存储服务收到请求时，它将获取输入查询参数，并使用与调用程序相同的方法来创建签名。 然后比较这两个签名。 如果它们相符，则存储服务可以检查存储服务版本以确保它有效、检查当前日期和时间是在指定时段内、确保请求的访问权限对应于发出的请求，等等。

以上述 URL 为例，如果 URL 指向文件而不是 Blob，此请求会失败，因为它指定共享访问签名适用于 Blob。 如果调用的 REST 命令是更新 Blob，则该命令会失败，因为共享访问签名指定只准许读访问权限。

#### <a name="types-of-shared-access-signatures"></a>共享访问签名的类型

* 服务级别 SAS 可用于访问存储帐户中的特定资源。 例如，检索文件系统中的一系列文件或下载某个文件。
* 帐户级别 SAS 可用于访问服务级别 SAS 可用的任何功能。 此外，它可以为服务级别 SAS 不准许的资源提供选项，例如创建文件系统的功能。

#### <a name="creating-a-sas-uri"></a>创建 SAS URI

1. 可以根据需要创建 URI，每次都定义所有查询参数。

   该方法十分灵活，但如果每次都有一组类似的逻辑参数，使用存储访问策略是个不错的想法。
2. 可以为整个文件系统、文件共享、表或队列创建存储访问策略。 然后使用此策略作为所创建的 SAS URI 的基础。 可以轻松吊销基于存储访问策略的权限。 可以对每个文件系统、队列、表或文件共享最多定义五个策略。

   例如，如果要让许多人读取特定文件系统中的 Blob，则可以创建存储访问策略，表示“提供读访问权限”以及每次都一样的任何其他设置。 然后，可以使用存储访问策略的设置，并指定过期日期/时间，来创建 SAS URI。 这样做的优点是不需要每次指定所有查询参数。

#### <a name="revocation"></a>吊销

假设 SAS 已泄露，或者要基于公司安全或法规遵循要求更改 SAS。 如何使用该 SAS 吊销对资源的访问权限？ 这取决于 SAS URI 的创建方式。

如果使用即席 URI，将有三个选项。 可以颁发具有短期过期策略的 SAS 令牌，然后等待 SAS 过期。 可以重命名或删除资源（假设令牌范围只限于单个对象）。 可以更改存储帐户密钥。 根据使用该存储帐户的服务数目而定，最后一个选项可能造成很大的影响，而且在没有任何规划的情况下可能达不到想要的效果。

如果使用派生自存储访问策略的 SAS，就可以通过吊销存储访问策略来删除访问权限 – 只能在它已经完全过期时进行更改，或完全删除它。 这会立即生效，并使每个使用该存储访问策略创建的 SAS 失效。 更新或删除存储访问策略可能会影响通过 SAS 访问该特定文件系统、文件共享、表或队列的用户，但如果将写入客户端，使得他们可在旧的 SAS 变成无效时请求一个新的 SAS，则这会正常运行。

由于使用派生自存储访问策略的 SAS 可以立即吊销该 SAS，因此建议的最佳做法是始终使用存储访问策略（如果可能）。

#### <a name="resources"></a>资源

有关使用共享访问签名和存储访问策略的详细信息和示例，请参阅以下文章：

* 下面是参考文章。

  * [服务 SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    此文提供有关使用服务级别 SAS 配合 Blob、队列、表范围和文件的示例。
  * [Constructing a Service SAS](https://msdn.microsoft.com/library/dn140255.aspx)（构造服务 SAS）
  * [Constructing an account SAS](https://msdn.microsoft.com/library/mt584140.aspx)（构造帐户 SAS）
* 这些教程介绍了如何使用 .NET 客户端库创建共享访问签名和存储访问策略。

  * [使用共享访问签名 (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [共享访问签名，第 2 部分：创建 SAS 并将其用于 Blob 服务](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    此文包含 SAS 模型的说明、共享访问签名的示例，以及 SAS 用法最佳实践的建议。 此外介绍了如何吊销授予的权限。

* Authentication

  * [Authentication for the Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx)（Azure 存储服务的身份验证）
* 共享访问签名入门教程

  * [SAS 入门教程](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>传输中加密

### <a name="transport-level-encryption--using-https"></a>传输级加密 – 使用 HTTPS

为确保 Azure 存储数据安全而要采取的另一个措施是在客户端与 Azure 存储之间加密数据。 第一条建议是始终使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 协议，这可确保通过公共 Internet 进行安全通信。

若要获得安全的信道，在调用 REST API 或访问存储中的对象时，应该始终使用 HTTPS。 此外，除了可用于委派对 Azure 存储对象的访问权限，**共享访问签名**还可用于指定在使用共享访问签名时只能使用 HTTPS 协议，确保任何使用 SAS 令牌发出链接的人都使用正确的协议。

在调用 REST API 来访问存储帐户中的对象时，可以通过为存储帐户启用 [需要安全传输](../storage-require-secure-transfer.md) 来强制使用 HTTPS。 启用此设置后，使用 HTTP 的连接将被拒绝。

## <a name="encryption-at-rest"></a>静态加密

### <a name="storage-service-encryption-sse"></a>存储服务加密 (SSE)

针对所有存储帐户启用 SSE，并且不能禁用。 将数据写入 Azure 存储时，SSE 自动加密数据。 从 Azure 存储读取数据时，Azure 存储会在返回数据之前将其解密。 SSE 帮助保护数据，而无需修改代码或将代码添加到任何应用程序。

你可以使用 Microsoft 托管的密钥或你自己的自定义密钥。 Microsoft 生成托管密钥，并根据 Microsoft 内部策略的定义管理其安全存储和定期轮转。 有关使用自定义密钥的详细信息，请参阅[在 Azure 密钥保管库中使用客户托管密钥进行存储服务加密](storage-service-encryption-customer-managed-keys.md)。
