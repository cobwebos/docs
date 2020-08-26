---
title: Azure 存储资源管理器安全指南 |Microsoft Docs
description: Azure 存储资源管理器的安全指南
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: d9dea7cd0cc22cc8a1e0aa5c93ece76d689de0e0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835435"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure 存储资源管理器安全指南

Microsoft Azure 存储资源管理器使你能够在 Windows、macOS 和 Linux 上安全可靠地使用 Azure 存储数据。 按照这些准则，你可以确保数据始终受到保护。

## <a name="general"></a>常规

- **始终使用最新版本的存储资源管理器。** 存储资源管理器版本可能包含安全更新。 保持最新状态有助于确保总体安全性。
- **仅连接到你信任的资源。** 从不受信任的源下载的数据可能是恶意的，将数据上载到不受信任的源可能会导致数据丢失或被盗。
- **尽可能使用 HTTPS。** 默认情况下，存储资源管理器使用 HTTPS。 某些方案允许使用 HTTP，但 HTTP 只能作为最后的手段使用。
- **确保仅向需要权限的人员提供所需的权限。** 向任何人授予对资源的访问权限时，应避免过于宽松。
- **执行关键操作时请小心。** 某些操作（如删除和覆盖）是不可逆的，并且可能会导致数据丢失。 执行这些操作之前，请确保使用的是正确的资源。

## <a name="choosing-the-right-authentication-method"></a>选择正确的身份验证方法

存储资源管理器提供了不同的方式来访问 Azure 存储资源。 无论选择哪种方法，都建议您这样做。

### <a name="azure-ad-authentication"></a>Azure AD 身份验证

若要访问 Azure 存储资源，最简单、最安全的方法是使用 Azure 帐户登录。 登录使用 Azure AD 身份验证，这允许你：

- 向特定用户和组授予访问权限。
- 随时撤销对特定用户和组的访问权限。
- 强制执行访问条件，如需要多重身份验证。

建议尽可能使用 Azure AD 身份验证。

本部分介绍了两种基于 Azure AD 的技术，这些技术可用于保护存储资源。

#### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

Azure [RBAC)  (azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)，可让你对 azure 资源进行精细的访问控制。 可以从 Azure 门户管理 RBAC 角色和权限。

存储资源管理器支持 RBAC 访问存储帐户、Blob 和队列。 如果需要访问文件共享或表，则需要分配允许权限列出存储帐户密钥的 RBAC 角色。

#### <a name="access-control-lists-acls"></a>访问控制列表 (ACL)

[访问控制列表 (acl) ](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)使你可以控制 ADLS Gen2 blob 容器中的文件和文件夹级别的访问权限。 你可以使用存储资源管理器管理 Acl。

### <a name="shared-access-signatures-sas"></a>共享访问签名 (SAS)

如果无法使用 Azure AD 身份验证，我们建议使用共享访问签名。 使用共享访问签名，你可以：

- 提供对安全资源的匿名限制访问。
- 如果从共享访问策略生成的 SAS)  (，则立即吊销 SAS。

但使用共享访问签名时，不能：

- 限制可以使用 SAS 的用户。 具有有效 SAS 的任何人都可以使用它。
- 如果未从共享访问策略 (SAP) 生成 SAS，请将其撤消。

在存储资源管理器中使用 SAS 时，我们建议遵循以下准则：

- **限制 SAS 令牌和 Uri 的分布。** 仅向受信任的个人分发 SAS 令牌和 Uri。 限制 SAS 分发可降低 SAS 的滥用机会。
- **仅使用你信任的实体中的 SAS 令牌和 Uri。**
- **尽可能使用共享访问策略 (SAP) 生成 SAS 令牌和 Uri 时。** 基于共享访问策略的 SAS 比裸 SAS 更安全，因为可以通过删除 SAP 来撤销 SAS。
- **使用最小资源访问权限和权限生成令牌。** 最小权限限制了在误用 SAS 时可能会发生的潜在损害。
- **生成仅在必要时才有效的令牌。** 简短的生命周期对于空的 SAS 尤为重要，因为在生成后，无法对其进行撤消。

> [!IMPORTANT]
> 共享 SAS 令牌和 Uri 以进行故障排除（例如在服务请求或 bug 报告中）时，始终会至少对 SAS 的签名部分进行标记。

### <a name="storage-account-keys"></a>存储帐户密钥

存储帐户密钥授予对存储帐户中的服务和资源的无限制访问权限。 出于此原因，我们建议限制使用密钥来访问存储资源管理器中的资源。 请改用 RBAC 功能或 SAS 来提供访问权限。

某些 RBAC 角色授予检索存储帐户密钥的权限。 具有这些角色的人员可以有效地绕过 RBAC 允许或拒绝的权限。 除非有必要，否则建议不要授予此权限。

存储资源管理器将尝试使用存储帐户密钥（如果可用）来对请求进行身份验证。 你可以在 "设置" (服务 "中禁用此功能 **> 存储帐户 > 禁用密钥**) 的使用。 某些功能不支持 RBAC，如使用经典存储帐户。 此类功能仍需要密钥，不受此设置的影响。

如果必须使用密钥来访问存储资源，我们建议遵循以下准则：

- **不要与任何人共享帐户密钥。**
- **将你的存储帐户密钥视为密码。** 如果必须使密钥可访问，请使用安全存储解决方案，如[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

> [!NOTE]
> 如果你认为某个存储帐户密钥被错误共享或分发，则可以从 Azure 门户为你的存储帐户生成新密钥。

### <a name="public-access-to-blob-containers"></a>对 blob 容器的公共访问权限

存储资源管理器允许你修改 Azure Blob 存储容器的访问级别。 非专用 blob 容器允许任何人对这些容器中的数据进行匿名读取访问。

为 blob 容器启用公共访问时，我们建议遵循以下准则：

- **不要启用对可能包含任何敏感数据的 blob 容器的公共访问权限。** 请确保 blob 容器中的所有专用数据都可用。
- **请勿将任何可能的敏感数据上传到具有 Blob 或容器访问权限的 blob 容器。** 

## <a name="next-steps"></a>后续步骤

- [安全建议](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)
