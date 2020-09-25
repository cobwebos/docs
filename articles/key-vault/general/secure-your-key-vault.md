---
title: 保护对密钥保管库的访问
description: Azure Key Vault 的访问模型，包括 Active Directory 身份验证和资源终结点。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 9516a32e89b9ad671cf705c8f520c73e28801c19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320585"
---
# <a name="secure-access-to-a-key-vault"></a>保护对密钥保管库的访问

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 因为此数据是敏感数据和业务关键数据，所以需要保护对密钥保管库的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了 Key Vault 访问模型。 其中介绍了身份验证和授权，以及如何保护对密钥保管库的访问。

## <a name="access-model-overview"></a>访问模型概述

可通过以下两个接口来控制对密钥保管库的访问：**管理平面**和**数据平面**。 管理平面用于管理密钥保管库本身。 此平面中的操作包括创建和删除密钥保管库、检索密钥保管库属性以及更新访问策略。 数据平面用于处理密钥保管库中存储的数据。 可以添加、删除和修改密钥、机密及证书。

若要在任一平面中访问密钥保管库，所有调用方（用户或应用程序）都必须进行适当的身份验证并拥有适当的授权。 身份验证可确定调用方的身份。 授权可确定调用方能够执行的操作。

对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用 Azure 基于角色的访问控制 (RBAC)，而数据平面使用 Key Vault 访问策略和 Azure RBAC（预览版）。

## <a name="active-directory-authentication"></a>Active Directory 身份验证

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 两个平面中的所有调用方都必须在此租户中注册并进行身份验证，然后才能访问该密钥保管库。 在这两种情况下，应用程序可以通过两种方式访问密钥保管库：

- 仅应用程序：该应用程序表示一个服务或后台作业。 对于定期需要从密钥保管库访问证书、密钥或机密的应用程序，此标识是最常见的方案。 要使此方案正常运行， `objectId` 必须在访问策略中指定应用程序的，并且 `applicationId` _不_ 能指定或必须是 `null` 。
- 仅用户：用户从租户中注册的任何应用程序访问密钥保管库。 此类访问的示例包括 Azure PowerShell 和 Azure 门户。 要使此方案正常工作， `objectId` 必须在访问策略中指定用户的，且 `applicationId` 不得指定， _not_或者必须是 `null` 。
- 应用程序和用户（有时称为“复合标识”）：用户需要从特定应用程序访问密钥保管库，并且该应用程序必须使用代理身份验证 (OBO) 流来模拟用户。 要使此方案起作用，必须在访问策略中指定 `applicationId` 和 `objectId`。 `applicationId`标识所需的应用程序，并 `objectId` 标识该用户。 目前，此选项不可用于数据平面 Azure RBAC (预览) 。

在所有类型的访问中，应用程序都使用 Azure AD 进行身份验证。 应用程序根据应用程序类型使用任何[支持的身份验证方法](../../active-directory/develop/authentication-scenarios.md)。 应用程序通过获取平面中资源的令牌来授予访问权限。 资源是管理平面或数据平面中基于 Azure 环境的终结点。 应用程序使用令牌并向密钥保管库发送 REST API 请求。 若要了解详细信息，请查看[整个身份验证流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

对这两种平面使用单一身份验证机制模型具有多个优点：

- 组织可以集中控制对其组织中的所有密钥保管库的访问。
- 离职的用户会立即失去对组织中所有密钥保管库的访问权限。
- 组织可以通过 Azure AD 中的选项自定义身份验证（例如，启用多重身份验证以提高安全性）。

## <a name="resource-endpoints"></a>资源终结点

应用程序通过终结点访问平面。 两个平面的访问控制独立运行。 若要授权应用程序使用密钥保管库中的密钥，可以使用 Key Vault 访问策略或 Azure RBAC（预览版）授予数据平面访问权限。 若要授予用户对密钥保管库属性和标记的读取访问权限，但不授予对数据（密钥、机密或证书）的访问权限，请使用 RBAC 授予管理平面访问权限。

下表显示了用于管理平面和数据平面的终结点。

| 访问&nbsp;平面 | 访问终结点 | 操作 | 访问&nbsp;控制机制 |
| --- | --- | --- | --- |
| 管理平面 | **全球：**<br> management.azure.com:443<br><br> **Azure 中国世纪互联：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 | 创建、读取、更新和删除密钥保管库<br><br>设置密钥保管库访问策略<br><br>设置密钥保管库标记 | Azure RBAC |
| 数据平面 | **全球：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 中国世纪互联：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 密钥：加密、解密、wrapKey、unwrapKey、签名、验证、获取、列出、创建、更新、导入、删除、恢复、备份、还原、清除<br><br> 证书： managecontacts、getissuers、listissuers、setissuers、deleteissuers、manageissuers、get、list、create、import、update、delete、recover、backup、restore、delete<br><br>  机密：获取、列出、设置、删除、恢复、备份、还原、清除 | Key Vault 访问策略或 Azure RBAC (预览版) |

## <a name="management-plane-and-azure-rbac"></a>管理平面和 Azure RBAC

在管理平面中，你会使用 Azure 基于角色的访问控制 (Azure RBAC) 对调用方可以执行的操作进行授权。 在 Azure RBAC 模型中，每个 Azure 订阅都有一个 Azure AD 实例。 可以从此目录向用户、组和应用程序授予访问权限。 授予访问权限以管理 Azure 订阅中使用 Azure 资源管理器部署模型的资源。

可以在资源组中创建密钥保管库，并使用 Azure AD 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可通过分配适当的 Azure 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 Azure 角色：

- **订阅**：在订阅级别分配的 Azure 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 Azure 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 Azure 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>数据平面和访问策略

可以通过为密钥保管库设置 Key Vault 访问策略，来授予数据平面访问权限。 若要设置这些访问策略，用户、组或应用程序必须具有该密钥保管库管理平面的 `Contributor` 权限。

可以向用户、组或应用程序授予对密钥保管库中的密钥或机密执行特定操作的访问权限。 密钥保管库最多支持 1024 个密钥保管库访问策略条目。 若要向多个用户授予对数据平面的访问权限，创建一个 Azure AD 安全组，并将用户添加到该组。

可在此处查看保管库和机密操作的完整列表：[Key Vault 操作参考](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> 密钥保管库访问策略单独授予对密钥、机密和证书的权限。  密钥、机密或证书的访问权限是保管库级别的。 

> [!IMPORTANT]
> Key Vault 访问策略适用于保管库级别。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。
密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 
>

## <a name="data-plane-and-azure-rbac-preview"></a>数据平面和 Azure RBAC（预览版）

Azure 基于角色的访问控制是另一种用于控制对 Azure Key Vault 数据平面的访问权限的权限模型，可在单个密钥保管库上启用。 Azure RBAC 权限模型是排他的，一旦设置，保管库访问策略就会变为非活动状态。 Azure Key Vault 定义了一组内置的 Azure 角色，它们包含用于访问密钥、机密或证书的通用权限集。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅级别、资源组、密钥保管库，或者单个密钥、机密或证书。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

相对于使用保管库访问策略，使用 Azure RBAC 权限的主要优点是集中式访问控制管理，并且它已与 Privileged Identity Management (PIM) 集成。 Privileged Identity Management 提供基于时间和基于审批的角色激活，用于缓解所关注资源上出现的访问权限过度、不必要或滥用的风险。


## <a name="firewalls-and-virtual-networks"></a>防火墙和虚拟网络

可以配置防火墙和虚拟网络规则以提供额外的安全层。 可以将 Key Vault 防火墙和虚拟网络配置为默认拒绝访问来自所有网络的流量（包括 Internet 流量）。 可以向来自特定 Azure 虚拟网络和公共 Internet IP 地址范围的流量授予访问权限，为应用程序构建安全的网络边界。

下面是此服务终结点的一些用法示例：

* 使用 Key Vault 存储加密密钥、应用程序机密和证书，并希望阻止从公共 Internet 访问 Key Vault。
* 你希望限制访问 Key Vault，以便只有你的应用程序或指定的少部分主机才能连接到 Key Vault。
* 你有一个在 Azure 虚拟网络中运行的应用程序，并且此虚拟网络限制了所有的入站和出站流量。 应用程序仍需连接到 Key Vault，以获取机密或证书，或者使用加密密钥。

> [!NOTE]
> Key Vault 防火墙和虚拟网络规则仅适用于 Key Vault 数据平面。 Key Vault 控制平面操作（例如创建、删除和修改操作，设置访问策略，设置防火墙和虚拟网络规则）不受防火墙和虚拟网络规则的影响。

## <a name="private-endpoint-connection"></a>专用终结点连接

如果需要完全阻止公开 Key Vault 公开，则可以使用 Azure 私有终结点。 Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

使用 Azure 服务的专用链接的常见方案：

- **以私密方式访问 Azure 平台上的服务**：无需在源或目标上使用公共 IP 地址，即可将虚拟网络连接到 Azure 中的服务。 服务提供商可在自己的虚拟网络中呈现其服务，而使用者可在其本地虚拟网络中访问这些服务。 专用链接平台将通过 Azure 主干网络处理使用者与服务之间的连接。 
 
- **本地网络和对等互连的网络**：使用专用终结点通过 ExpressRoute 专用对等互连、VPN 隧道和对等互连的虚拟网络从本地访问 Azure 中运行的服务。 无需设置公共对等互连或遍历 Internet 即可访问服务。 专用链接可让客户安全地将工作负荷迁移到 Azure。
 
- **防范数据泄露**：专用终结点映射到 PaaS 资源的某个实例，而不是映射到整个服务。 使用者只能连接到特定的资源。 对服务中任何其他资源的访问将遭到阻止。 此机制可以防范数据泄露风险。 
 
- **全球覆盖**：以私密方式连接到在其他区域中运行的服务。 使用者的虚拟网络可以位于区域 A，而且可以连接到区域 B 中专用链接后面的服务。  
 
- **扩展到自己的服务**：实现相同的体验和功能，以私密方式将服务呈现给 Azure 中的使用者。 将服务放在标准 Azure 负载均衡器的后面即可为其启用专用链接。 然后，使用者可以使用其自己的虚拟网络中的专用终结点直接连接到你的服务。 可以使用审批调用流来管理这些连接请求。 Azure 专用链接适用于属于不同 Azure Active Directory 租户的使用者和服务。 

## <a name="example"></a>示例

在此示例中，我们将开发一个应用程序，该应用程序使用证书来实现 TLS/SSL、使用 Azure 存储进行数据存储，并使用 RSA 2,048 位密钥来加密 Azure 存储中的数据。 我们的应用程序在 Azure 虚拟机 (VM)（或虚拟机规模集）中运行。 我们可以使用密钥保管库来存储应用程序机密。 我们可以存储应用程序用于通过 Azure AD 进行身份验证的启动证书。

我们需要对以下存储密钥和机密的访问权限：
- **TLS/SSL 证书**：用于 TLS/SSL。
- **存储密钥**：无法访问存储帐户。
- **RSA 2048 位密钥**：由 Azure 存储用来包装/解包数据加密密钥。
- 应用程序托管标识：用于使用 Azure AD 进行身份验证。 授予对 Key Vault 的访问权限后，应用程序可以提取存储密钥和证书。

我们需要定义以下角色，以指定可以管理、部署和审核应用程序的用户：
- **安全团队**：CSO（首席安全官）办公室中的 IT 人员或类似参与者。 安全团队负责机密的适当保管。 机密可能包括 TLS/SSL 证书、用于加密的 RSA 密钥、连接字符串和存储帐户密钥。
- **开发人员和操作人员**：开发应用程序并在 Azure 中进行部署的人员。 此团队的成员不属于安全人员。 他们不应有权访问 TLS/SSL 证书和 RSA 密钥等敏感数据。 仅他们部署的应用程序才应有权访问敏感数据。
- **审核员**：此角色适用于不属于开发人员或一般 IT 人员的参与者。 他们评审证书、密钥和机密的使用及维护，确保符合安全标准。

还有一个超出我们应用程序范围的角色：订阅（或资源组）管理员。 订阅管理员为安全团队设置初始访问权限。 他们通过使用具有应用程序所需资源的资源组来授予安全团队访问权限。

我们需要为角色的以下操作进行授权：

**安全团队**
- 创建密钥保管库。
- 打开密钥保管库日志记录。
- 添加密钥和机密。
- 为灾难恢复创建密钥备份。
- 设置 Key Vault 访问策略并分配角色，以便向用户和应用程序授予执行特定操作的权限。
- 定期滚动密钥和机密。

**开发人员和操作人员**
- 从安全团队获取有关启动证书和 TLS/SSL 证书（指纹）、存储密钥（机密 URI）以及用于包装/解包的 RSA 密钥（密钥 URI）的参考资料。
- 以编程方式开发和部署用于访问证书和机密的应用程序。

**审核人员**
- 查看密钥保管库日志以确认正确使用了密钥和机密且符合数据安全标准。

下表总结了我们的角色和应用程序的访问权限。

| 角色 | 管理平面权限 | 数据平面权限 - 保管库访问策略 | 数据平面权限 - Azure RBAC（预览版）  |
| --- | --- | --- | --- |
| 安全团队 | 密钥保管库参与者 | 证书：所有操作 <br> 密钥：所有操作 <br> 机密：所有操作 | 密钥保管库管理员（预览版） |
| 开发人员和&nbsp;操作人员 | 密钥保管库部署权限<br><br> **注意**：此权限允许已部署的 VM 从密钥保管库提取机密。 | 无 | 无 |
| 审核人员 | 无 | 证书：list <br> 密钥：列出<br>机密：列出<br><br> **注意**：此权限让审核员能够检查日志中未发出的密钥和机密的属性（标记、激活日期、到期日期）。 | 密钥保管库读取者（预览版） |
| Azure 存储帐户 | None | 密钥：get、list、wrapKey、unwrapKey <br> | 密钥保管库加密服务加密 |
| 应用程序 | 无 | 机密：get、list <br> 证书：get、list | 密钥保管库读取者（预览版）、密钥保管库机密用户（预览版） |

三个团队角色需要访问其他资源的权限以及密钥保管库权限。 若要部署 VM（或 Azure 应用服务的 Web 应用功能），开发人员和操作人员需要部署访问权限。 审核员需要具有对存储密钥保管库日志的存储帐户的“读取”访问权限。

我们的示例介绍了一个简单的方案。 现实方案可能更复杂。 可以根据需要调整密钥保管库的权限。 我们假设安全团队提供密钥和机密引用（URI 和指纹），开发运营员工在其应用程序中使用这些引用。 开发人员和操作员不需要任何数据平面访问权限。 我们将重点放在如何保护密钥保管库上。

> [!NOTE]
> 此示例介绍如何在生产中锁定密钥保管库访问。 开发人员应具有其自己的订阅或资源组，他们具有这些订阅或资源组的完整权限，可管理其用来开发应用程序的保管库、VM 和存储帐户。

## <a name="resources"></a>资源

* [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>后续步骤

[对 Azure Key Vault 进行身份验证](authentication.md)

[分配 Key Vault 访问策略](assign-access-policy-portal.md)

[分配用于访问密钥、机密和证书的 Azure 角色](rbac-guide.md)

[配置 Key Vault 防火墙和虚拟网络](network-security.md)

[建立与 Key Vault 的专用链接连接](private-link-service.md)
