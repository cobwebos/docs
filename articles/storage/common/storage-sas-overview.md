---
title: 使用共享访问签名 (SAS) 授予对数据的有限访问权限
titleSuffix: Azure Storage
description: 了解使用共享访问签名 (SAS) 委托对 Azure 存储资源（包括 Blob、队列、表和文件）的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 680c1b036b9b41edb1115f478fd5dc8f63ea1d02
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688046"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限

使用共享访问签名 (SAS) 可以安全委托对存储帐户中的资源的访问权限，而不会损害数据的安全性。 使用 SAS 可以精细控制客户端访问数据的方式。 你可以控制客户端可以访问哪些资源、客户端对这些资源拥有哪些权限、SAS 的有效期，以及其他参数。

## <a name="types-of-shared-access-signatures"></a>共享访问签名的类型

Azure 存储支持三种类型的共享访问签名：

- **用户委托 SAS。** 用户委托 SAS 使用 Azure Active Directory (Azure AD) 凭据以及为 SAS 指定的权限进行保护。 用户委托 SAS 仅适用于 Blob 存储。

    有关用户委托 SAS 的详细信息，请参阅[创建用户委托 sas （REST API）](/rest/api/storageservices/create-user-delegation-sas)。

- **服务 SAS。** 使用存储帐户密钥保护的服务 SAS。 服务 SAS 仅委托 Azure 存储服务之一中的资源访问： Blob 存储、队列存储、表存储或 Azure 文件。 

    有关服务 SAS 的详细信息，请参阅[创建服务 SAS (REST API)](/rest/api/storageservices/create-service-sas)。

- **帐户 SAS。** 帐户 SAS 使用存储帐户密钥进行保护。 帐户 SAS 可委派对一个或多个存储服务中的资源的访问权限。 通过服务或用户委托 SAS 提供的所有操作也可以通过帐户 SAS 提供。 此外，使用帐户 SAS 还可以委托对在服务级别应用的操作（例如“获取/设置服务属性”和“获取服务统计信息”操作）的访问权限。******** 还可以委派对 blob 容器、表、队列和文件共享执行读取、写入和删除操作的访问权限，而这是服务 SAS 所不允许的。 

    有关帐户 SAS 的详细信息，请参阅[创建帐户 SAS (REST API)](/rest/api/storageservices/create-account-sas)。

> [!NOTE]
> Microsoft 建议尽可能使用 Azure AD 凭据作为最佳安全方案，而不是使用帐户密钥，这样可以更容易地受到威胁。 当应用程序设计要求使用共享访问签名来访问 Blob 存储时，请使用 Azure AD 凭据创建用户委托 SAS （如果可能）以实现高级安全性。

共享访问签名可以采取以下两种形式的一种：

- **即席 SAS：** 当你创建一个即席 SAS 时，该 SAS 的开始时间、到期时间和权限都在 SAS URI 中指定（如果省略了开始时间，则为默示的权限）。 任何类型的 SAS 都可以用作临时 SAS。
- **具有存储访问策略的服务 SAS：** 存储访问策略在资源容器（可以是 blob 容器、表、队列或文件共享）上定义。 可以使用存储访问策略来管理一个或多个服务共享访问签名的约束。 将某个服务 SAS 与某个存储访问策略关联时，该 SAS 将继承对该存储访问策略定义的约束 &mdash; 开始时间、过期时间和权限。

> [!NOTE]
> 用户委托 SAS 或帐户 SAS 必须是一个临时 SAS。 用户委托 SAS 或帐户 SAS 不支持存储访问策略。

## <a name="how-a-shared-access-signature-works"></a>共享访问签名的工作方式

共享访问签名是一种签名的 URI，它指向一个或多个存储资源并且包括包含一组特殊查询参数的令牌。 该令牌指示客户端可以如何访问资源。 作为一个查询参数，签名是基于 SAS 参数构造的，已通过用来创建该 SAS 的密钥签名。 Azure 存储使用该签名授予对存储资源的访问权限。

### <a name="sas-signature"></a>SAS 签名

可通过以下两种方式之一对 SAS 进行签名：

- 使用 Azure Active Directory （Azure AD）凭据创建的*用户委托密钥*。 用户委托 SAS 使用用户委托密钥进行签名。

    若要获取用户委托密钥并创建 SAS，必须为 Azure AD 安全主体分配一个基于角色的访问控制（RBAC）角色，其中包括 storageAccounts/ **/blobServices/generateUserDelegationKey**操作。 有关具有获取用户委托密钥权限的 RBAC 角色的详细信息，请参阅[创建用户委托 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)。

- 使用存储帐户密钥。 服务 SAS 和帐户 SAS 都是使用存储帐户密钥签名的。 若要创建使用帐户密钥签名的 SAS，应用程序必须有权访问该帐户密钥。

### <a name="sas-token"></a>SAS 令牌

SAS 令牌是在客户端生成的字符串，例如，使用某个 Azure 存储客户端库生成。 Azure 存储不会以任何方式跟踪 SAS 令牌。 可在客户端侧创建无限数量的 SAS 令牌。 创建 SAS 后，可将其分发到需要访问存储帐户中的资源的客户端应用程序。

当客户端应用程序提供 Azure 存储的 SAS URI 作为请求的一部分时，服务将检查 SAS 参数和签名，以验证该 SAS 是否可用于授权请求。 如果服务验证签名有效，则对请求进行授权。 否则，将拒绝请求，错误代码为 403（已禁止）。

下面是服务 SAS URI 的一个示例，其中显示了资源 URI 和 SAS 令牌：

![服务 SAS URI 的组成部分](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>何时使用共享访问签名

若要为任何客户端提供对存储帐户中的资源的安全访问权限（否则该客户端无权访问这些资源），请使用 SAS。

SAS 通常适用于用户需要在存储帐户中读取和写入其数据的服务情形。 在存储帐户存储用户数据的情形中，有两种典型的设计模式：

1. 客户端通过执行身份验证的前端代理服务上传和下载数据。 此前端代理服务的优势在于允许验证业务规则，但对于大量数据或大量事务，创建可扩展以匹配需求的服务可能成本高昂或十分困难。

   ![方案示意图：前端代理服务](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. 轻型服务按需对客户端进行身份验证，并生成 SAS。 客户端应用程序收到 SAS 后，可以直接使用 SAS 定义的权限根据 SAS 允许的间隔访问存储帐户资源。 SAS 减少了通过前端代理服务路由所有数据的需要。

   ![方案示意图：SAS 提供程序服务](./media/storage-sas-overview/sas-storage-provider-service.png)

许多实际服务可能会混合使用这两种方法。 例如，可能通过前端代理对某些数据进行处理和验证，同时使用 SAS 直接保存和/或读取其他数据。

此外，在某些方案的复制操作中，需要使用 SAS 来授权访问源对象：

- 将一个 Blob 复制到驻留在不同存储帐户中的另一个 Blob 时，必须使用 SAS 授予对源 Blob 的访问权限。 还可以选择使用 SAS 授予对目标 blob 的访问权限。
- 将一个文件复制到驻留在不同存储帐户中的另一个文件时，必须使用 SAS 授予对源文件的访问权限。 还可以选择使用 SAS 授予对目标文件的访问权限。
- 将一个 Blob 复制到一个文件，或将一个文件复制到一个 Blob 时，必须使用 SAS 授予对源对象的访问权限，即使源对象和目标对象驻留在同一存储帐户中也是如此。

## <a name="best-practices-when-using-sas"></a>使用 SAS 的最佳实践

在应用程序中使用共享访问签名时，需要知道以下两个可能的风险：

- 如果 SAS 泄露，则获取它的任何人都可以使用它，这可能会损害存储帐户。
- 如果提供给客户端应用程序的 SAS 到期并且应用程序无法从服务检索新 SAS，则可能会影响该应用程序的功能。

下面这些针对使用共享访问签名的建议可帮助降低这些风险：

- **始终使用 HTTPS** 创建或分发 SAS。 如果某一 SAS 通过 HTTP 传递并且被截取，则执行中间人攻击的攻击者能够读取 SAS、然后使用它，就像目标用户本可执行的操作一样，这可能会暴露敏感数据或者使恶意用户能够损坏数据。
- **尽可能使用用户委托 SAS。** 用户委托 SAS 为服务 SAS 或帐户 SAS 提供了高级安全。 使用 Azure AD 凭据保护用户委托 SAS，以便不需要将帐户密钥与代码一起存储。
- **为 SAS 准备好吊销计划。** 确保已做好在 SAS 透露时的应对准备。
- **定义服务 SAS 的存储访问策略。** 存储访问策略可让你选择撤消服务 SAS 的权限，且无需重新生成存储帐户密钥。 将针对 SAS 的到期时间设置为很久之后的某一时间（或者无限远），并且确保定期对其进行更新以便将到期时间移到将来的更远时间。
- **对临时 SAS 服务 SAS 或帐户 SAS 使用短期过期时间。** 这样，即使某一 SAS 泄露，它也只会在短期内有效。 如果无法参照某一存储访问策略，该行为尤其重要。 临时到期时间还通过限制可用于上传到它的时间来限制可以写入 Blob 的数据量。
- **如果需要，让客户端自动续订 SAS。** 客户端应在到期时间之前很久就续订 SAS，这样，即使提供 SAS 的服务不可用，客户端也有时间重试。 如果 SAS 旨在用于少量即时的短期操作，这些操作应在到期时间内完成，则上述做法可能是不必要的，因为不应续订 SAS。 但是，如果客户端定期通过 SAS 发出请求，则有效期可能就会起作用。 需要考虑的主要方面就是在以下两者间进行权衡：对短期 SAS 的需求（如前文所述）以及确保客户端尽早请求续订（以免在成功续订前因 SAS 到期而中断）。
- **要注意 SAS 开始时间。** 如果将 SAS 的开始时间设置为**现在**，则由于时钟偏移（根据不同计算机，当前时间中的差异），在前几分钟会暂时观察到失败。 通常，将开始时间至少设置为 15 分钟前。 或者根本不设置，这会使它在所有情况下都立即生效。 同样此原则也适用于到期时间 - 请记住，对于任何请求，在任一方向你可能会观察到最多 15 分钟的时钟偏移。 对于使用 2012-02-12 之前的 REST 版本的客户端，未参照某一存储访问策略的 SAS 的最大持续时间是 1 小时，指定超过 1 小时持续时间的任何策略都会失败。
- **请注意 SAS 日期时间格式。** 如果为 SAS 设置了开始时间和/或过期时间（例如，命令行实用程序 AzCopy），则需要日期时间格式为 "+% Y-% m-% dT% H:%M：% SZ"，具体包括使用 SAS 令牌进行工作所需的秒数。  
- **对要访问的资源要具体。** 一个安全性最佳实践是向用户提供所需最小权限。 如果某一用户仅需要对单个实体的读取访问权限，则向该用户授予对该单个实体的读取访问权限，而不要授予针对所有实体的读取/写入/删除访问权限。 如果 SAS 泄露，这也有助于降低损失，因为攻击者手中掌握的 SAS 的权限较为有限。
- **知道每次使用都会对帐户收费，包括通过 SAS 使用。** 如果你提供某个 Blob 的写入访问权限，则用户可以选择上传 200 GB 的 Blob。 如果还向用户提供了对 Blob 的读访问权限，他们可能会选择下载 Blob 10 次，对你产生 2 TB 的传出费用。 此外，提供受限权限，帮助降低恶意用户的潜在操作威胁。 使用短期 SAS 以便减少这一威胁（但要注意结束时间上的时钟偏移）。
- **验证使用 SAS 写入的数据。** 在某一客户端应用程序将数据写入存储帐户时，请记住对于这些数据可能存在问题。 如果应用程序要求在数据可供使用前对数据进行验证或授权，应该在写入数据后、但在应用程序使用这些数据前执行此验证。 这一实践还有助于防止损坏的数据或恶意数据写入帐户，这些数据可能是正常要求 SAS 的用户写入的，也可能是利用泄露的 SAS 的用户写入的。
- **知道何时不使用 SAS。** 有时，针对存储帐户执行特定操作所带来的风险超过了 SAS 所带来的好处。 对于此类操作，应创建一个中间层服务，该服务在执行业务规则验证、身份验证和审核后写入存储帐户。 此外，有时候以其他方式管理访问会更简单。 例如，如果想要使某一容器中的所有 Blob 都可以公开读取，则可以使该容器成为公共的，而不是为每个客户端都提供 SAS 来进行访问。
- **使用 Azure Monitor 和 Azure 存储日志来监视应用程序。** 可以使用 Azure Monitor 和存储分析日志记录来观察由于 SAS 提供程序服务中断或无意中删除存储访问策略而导致授权失败的任何高发情形。 有关详细信息，请参阅 [Azure Monitor 中的 Azure 存储指标](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)和 [Azure 存储分析日志记录](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="get-started-with-sas"></a>开始使用 SAS

若要开始使用共享访问签名，请参阅以下适用于每种 SAS 类型的文章。

### <a name="user-delegation-sas"></a>用户委托 SAS

- [使用 PowerShell 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [为具有 Azure CLI 的容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [使用 .NET 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>服务 SAS

- [使用 .NET 为容器或 Blob 创建服务 SAS](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>帐户 SAS

- [使用 .NET 创建帐户 SAS](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>后续步骤

- [使用共享访问签名委托访问权限 (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [创建服务 SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [创建帐户 SAS (REST API)](/rest/api/storageservices/create-account-sas)
