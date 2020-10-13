---
title: Azure 存储资源管理器安全指南 | Microsoft Docs
description: 有关 Azure 存储资源管理器的安全指南
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: e3bbe39077cf6d7781f7e11fde044cf272aa83e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714379"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure 存储管理器安全指南

Microsoft Azure 存储资源管理器可用于在 Windows、macOS 和 Linux 上轻松安全地处理 Azure 存储数据。 通过遵循这些准则，可以确保数据得到保护。

## <a name="general"></a>常规

- **始终使用最新版本的存储资源管理器。** 存储资源管理器版本可能包含安全更新。 保持最新状态有助于确保总体安全性。
- **仅连接到信任的资源。** 从不受信任的源下载的数据可能是恶意的，将数据上载到不受信任的源可能会导致数据丢失或被盗。
- **尽可能使用 HTTPS。** 默认情况下，存储资源管理器使用 HTTPS。 某些方案允许使用 HTTP，但 HTTP 只能作为最后的手段使用。
- **确保只向需要权限的人授予其所需的权限。** 向任何人授予对资源的访问权限时，应避免授予的权限超过所需权限。
- **执行关键操作时请小心。** 某些操作（如删除和覆盖）是不可逆的，可能会导致数据丢失。 执行这些操作之前，请确保使用的是正确的资源。

## <a name="choosing-the-right-authentication-method"></a>选择正确的身份验证方法

存储资源管理器提供多种访问 Azure 存储资源的方法。 无论选择哪种方法，请参考以下建议。

### <a name="azure-ad-authentication"></a>Azure AD 身份验证

访问 Azure 存储资源最简单、最安全的方法是使用 Azure 帐户登录。 通过使用 Azure AD 身份验证登录，可以：

- 向特定用户和组授予访问权限。
- 随时撤消对特定用户和组的访问权限。
- 强制实施访问条件，例如要求进行多重身份验证。

建议尽可能使用 Azure AD 身份验证。

本节介绍可用于保护存储资源的两种基于 Azure AD 的技术。

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure 基于角色的访问控制 (Azure RBAC)

[Azure 基于角色的访问控制 (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 提供对 Azure 资源的精细访问控制。 可以从 Azure 门户管理 Azure 角色和权限。

存储资源管理器支持 Azure RBAC 访问存储帐户、Blob 和队列。 如果需要访问文件共享或表，则需要分配授予权限以列出存储帐户密钥的 Azure 角色。

#### <a name="access-control-lists-acls"></a>访问控制列表 (ACL)

[访问控制列表 (ACL)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 允许在 ADLS Gen2 blob 容器中的文件和文件夹级别控制访问权限。 你可以使用存储资源管理器管理 ACL。

### <a name="shared-access-signatures-sas"></a>共享访问签名 (SAS)

如果无法使用 Azure AD 身份验证，建议使用共享访问签名。 使用共享访问签名，你可以：

- 提供对安全资源的匿名的有限访问权限。
- 如果从共享访问策略 (SAP) 生成 SAS，则立即吊销 SAS。

但使用共享访问签名，你不能：

- 限制可以使用 SAS 的用户。 任何拥有有效 SAS 的人都可以使用它。
- 如果未从共享访问策略 (SAP) 生成 SAS，则吊销 SAS。

在存储资源管理器中使用 SAS 时，建议遵循以下准则：

- **限制 SAS 令牌和 URI 的分发。** 仅向受信任的个人分发 SAS 令牌和 URI。 限制 SAS 分发可降低 SAS 的误用机会。
- **仅使用来自信任的实体的 SAS 令牌和 URI。**
- **如果可能，在生成 SAS 令牌和 URI 时使用共享访问策略 (SAP)。** 基于共享访问策略的 SAS 比裸 SAS 更安全，因为 SAS 可以通过删除 SAP 来撤销。
- **生成具有最小资源访问权限的令牌。** 最小权限限制了误用 SAS 时可能造成的潜在损害。
- **生成仅在必要时才有效的令牌。** 短生命周期对于裸 SAS 来说尤其重要，因为生成后，就没有办法将其撤销。

> [!IMPORTANT]
> 当为了进行故障排除而共享 SAS 令牌和 URI 时（例如在服务请求或 bug 报告中），请至少对 SAS 的签名部分进行修订。

### <a name="storage-account-keys"></a>存储帐户密钥

存储帐户密钥授予对存储帐户中的服务和资源的无限制访问。 因此，我们建议限制使用密钥访问存储资源管理器中的资源。 使用 Azure RBAC 功能或 SAS 提供访问权限。

某些 Azure 角色授予检索存储帐户密钥的权限。 具有这些角色的人员可以有效地绕过 Azure RBAC 授予或拒绝的权限。 除非有必要，否则建议不要授予此权限。

存储资源管理器将尝试使用存储帐户密钥（如果可用）来验证请求。 可以在“设置”（“服务”>“存储帐户”>“禁用密钥”）中禁用此功能。 某些功能不支持 Azure RBAC，如使用经典存储帐户。 此类功能仍然需要密钥，并不受此设置的影响。

如果必须使用密钥来访问存储资源，建议遵循以下准则：

- **不要与任何人共享帐户密钥。**
- **将存储帐户密钥视为密码。** 如果必须使密钥可被访问，请使用安全存储解决方案，如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

> [!NOTE]
> 如果你认为存储帐户密钥已被错误地共享或分发，则可以从 Azure 门户为存储帐户生成新密钥。

### <a name="public-access-to-blob-containers"></a>公开访问 blob 容器

在存储资源管理器中可以修改 Azure Blob 存储容器的访问级别。 在非专用 blob 容器中，任何人都可以匿名读取这些容器中的数据。

在为 blob 容器启用公共访问时，建议遵循以下准则：

- **不要对可能包含任何潜在敏感数据的 blob 容器启用公共访问权限。** 确保 blob 容器中没有任何专用数据。
- **请勿将任何潜在的敏感数据上载到具有 blob 或容器访问权限的 blob 容器中。** 

## <a name="next-steps"></a>后续步骤

- [安全建议](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)
