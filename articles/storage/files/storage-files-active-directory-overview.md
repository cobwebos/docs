---
title: 概述 - Azure 文件基于标识的授权
description: Azure 文件通过 Azure 活动目录域服务 （AD DS） 和活动目录支持通过 SMB（服务器消息块）进行基于身份的身份验证。 然后，已加入域的 Windows 虚拟机 (VM) 可使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536568"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>基于 Azure 文件的基于身份的 SMB 访问的身份验证支持的概述
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

要了解如何为 Azure 文件共享（预览）启用本地活动目录域服务身份验证，请参阅[通过 SMB 为 Azure 文件共享启用本地活动目录域服务身份验证](storage-files-identity-auth-active-directory-enable.md)。

要了解如何为 Azure 文件共享启用 Azure AD DS 身份验证，请参阅[在 Azure 文件上启用 Azure 活动目录域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="glossary"></a>术语表 
了解与 Azure 文件共享的 SMB 上通过 SMB 进行 Azure AD 域服务身份验证的一些关键术语很有帮助：

-   **Kerberos 身份验证**

    Kerberos 是一种身份验证协议，用于验证用户或主机的标识。 有关 Kerberos 的详细信息，请参阅 [Kerberos 身份验证概述](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **服务器消息块 (SMB) 协议**

    SMB 是行业标准网络文件共享协议。 也称为通用 Internet 文件系统或 CIFS。 有关 SMB 的详细信息，请参阅 [Microsoft SMB 协议和 CIFS协议概述](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure Active Directory (Azure AD)**

    Azure 活动目录 （Azure AD） 是 Microsoft 的多租户基于云的目录和标识管理服务。 Azure AD 将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。 Azure AD 加入的 Windows 虚拟机 （VM） 可以使用 Azure AD 凭据访问 Azure 文件共享。 有关详细信息，请参阅什么是[Azure 活动目录？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory 域服务 (Azure AD DS)**

    Azure AD DS 提供托管域服务，如域联接、组策略、LDAP 和 Kerberos/NTLM 身份验证。 这些服务与活动目录域服务完全兼容。 有关详细信息，请参阅[Azure 活动目录域服务](../../active-directory-domain-services/overview.md)。

- **本地活动目录域服务 （AD DS）**

    与 Azure 文件（预览）的本地活动目录域服务 （AD DS） 集成提供了存储目录数据，同时使其可供网络用户和管理员使用的方法。 通过登录身份验证和对目录中对象的访问控制，安全性与 AD DS 集成。 通过单个网络登录，管理员可以管理整个网络中的目录数据和组织，并且经过授权的网络用户可以访问网络上的任意位置的资源。 AD DS 通常被企业在本地环境中采用，AD DS 凭据用作访问控制的身份。 有关详细信息，请参阅[活动目录域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 基于角色的访问控制 (RBAC)**

    Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可通过向用户授予执行其作业所需的最少权限来管理对资源的访问权限。 有关 RBAC 的详细信息，请参阅[Azure 中什么是基于角色的访问控制 （RBAC）？](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>常见用例

Azure 文件上的基于标识的身份验证和支持最适合使用以下用例：

### <a name="replace-on-premises-file-servers"></a>替换本地文件服务器

弃用和替换分散的本地文件服务器是每个企业在 IT 现代化旅程中遇到的常见问题。 当您可以将数据迁移到 Azure 文件时，使用本地 AD DS（预览）身份验证的 Azure 文件共享是最适合的。 完全迁移将允许您利用高可用性和可扩展性优势，同时最大限度地减少客户端更改。 它为最终用户提供了无缝的迁移体验，因此他们可以继续使用其现有的域联接计算机使用相同的凭据访问其数据。

### <a name="lift-and-shift-applications-to-azure"></a>提升应用程序并将其移至 Azure

提升应用程序并将其移至云时，需要为数据保留相同的身份验证模型。 随着我们将基于身份的访问控制体验扩展到 Azure 文件共享，无需将应用程序更改为现代身份验证方法并加快云采用。 Azure 文件共享提供了与 Azure AD DS 或本地 AD DS（预览）集成以进行身份验证的选项。 如果计划是 100% 云原生，并最大限度地减少管理云基础架构的努力，Azure AD DS 将更适合作为完全托管的域服务。 如果您需要与 AD DS 功能完全兼容，您可能需要考虑通过在 VM 上自托管域控制器将 AD DS 环境扩展到云。 无论哪种方式，我们都提供灵活性，选择适合您业务需求的域服务。

### <a name="backup-and-disaster-recovery-dr"></a>备份和灾难恢复 （DR）

如果要将主文件存储保留在本地，Azure 文件共享可用作备份或 DR 的理想存储，以提高业务连续性。 您可以使用 Azure 文件共享从现有文件服务器备份数据，同时保留 Windows DACL。 对于 DR 方案，您可以配置身份验证选项，以支持故障转移时的正确访问控制实施。

## <a name="supported-scenarios"></a>支持的方案

下表总结了支持的 Azure 文件共享 Azure AD DS 和本地 AD DS（预览）的身份验证方案。 我们建议选择为客户端环境采用的域服务，以便与 Azure 文件集成。 如果已在本地或 Azure 中设置 AD DS（预览），设备已加入到 AD 的域，则应选择利用 AD DS（预览）进行 Azure 文件共享身份验证。 同样，如果您已经采用 Azure AD DS （GA），则应将它用于 Azure 文件共享身份验证。


|Azure AD DS 身份验证  | 本地 AD DS（预览）身份验证  |
|---------|---------|
|Azure AD DS 联接的 Windows 计算机可以通过 SMB 访问具有 Azure AD 凭据的 Azure 文件共享。     |本地 AD DS 联接的 Windows 计算机可以使用通过 SMB 同步到 Azure AD 的本地活动目录凭据访问 Azure 文件共享。         |

### <a name="unsupported-scenarios"></a>不支持的方案

- Azure AD DS 和本地 AD DS 身份验证不支持针对计算机帐户的身份验证。 您可以考虑改用服务登录帐户。
- Azure AD DS 身份验证不支持针对 Azure AD 加入的设备进行身份验证。

## <a name="advantages-of-identity-based-authentication"></a>基于身份的身份验证的优势
与使用共享密钥身份验证不同，基于标识的 Azure 文件身份验证提供了几个好处：

-   **使用本地 AD DS 和 Azure AD DS 将传统的基于标识的文件共享访问体验扩展到云**  
    如果计划提升应用程序并将其移至云，用 Azure 文件共享替换传统文件服务器，则可能需要应用程序使用本地 AD DS 或 Azure AD DS 凭据进行身份验证以访问文件数据。 Azure 文件支持使用本地 AD DS 或 Azure AD DS 凭据从本地 AD DS 或 Azure AD DS 域联接的 VM 通过 SMB 访问 Azure 文件共享。

-   **对 Azure 文件共享强制实施粒度访问控制**  
    您可以在共享、目录或文件级别向特定标识授予权限。 例如，假设多个团队使用一个 Azure 文件共享进行项目协作。 你可以向所有团队授予访问非敏感目录的权限，而仅授权财务团队访问含敏感财务数据的目录。 

-   **备份 Windows ACL（也称为 NTFS）以及您的数据**  
    可以使用 Azure 文件共享备份现有的本地文件共享。 当您通过 SMB 将文件共享备份到 Azure 文件共享时，Azure 文件会保留 ACL 和数据。

## <a name="how-it-works"></a>工作原理

Azure 文件共享支持 Kerberos 身份验证，以便与 Azure AD DS 或本地 AD DS（预览）集成。 在 Azure 文件共享上启用身份验证之前，必须先设置域环境。 对于 Azure AD DS 身份验证，应启用 Azure AD 域服务和域加入计划从中访问文件数据的 VM。 加入域的 VM 必须与 Azure AD DS 驻留在同一虚拟网络 （VNET） 中。 同样，对于本地 AD DS（预览）身份验证，您需要设置域控制器和域加入计算机或 VM。

当与在 VM 上运行的应用程序关联的标识尝试访问 Azure 文件共享中的数据时，请求将发送到 Azure AD DS 以验证该标识。 如果身份验证成功，Azure AD DS 将返回 Kerberos 令牌。 应用程序发送包含 Kerberos 令牌的请求，Azure 文件共享使用该令牌授权请求。 Azure 文件共享仅接收令牌，不保留 Azure AD DS 凭据。 本地 AD DS 身份验证的工作方式类似，AD DS 提供 Kerberos 令牌。

![屏幕截图显示了通过 SMB 进行 Azure AD 身份验证的关系图](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>启用基于身份的身份验证

您可以使用 Azure AD DS 或本地 AD DS（预览）为新存储帐户和现有存储帐户上的 Azure 文件共享启用基于身份的身份验证。 存储帐户上只能使用一个域服务来进行文件访问身份验证，该身份验证适用于帐户中的所有文件共享。 在本文"[在 Azure 文件上启用 Azure 活动目录域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)"一文中，有关使用 Azure AD DS 设置文件共享以进行身份验证的详细指南，以及另一篇文章中的本地 AD DS（预览）指南，[即为 Azure 文件共享启用本地活动目录域服务身份验证](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>为 Azure 文件配置共享级别权限

启用 Azure AD DS 或本地 AD DS（预览）身份验证后，可以使用内置 RBAC 角色或为 Azure AD 标识配置自定义角色，并将访问权限分配给存储帐户中的任何文件共享。 分配的权限允许授予的标识仅访问共享，没有其他内容，甚至无法访问根目录。 您仍然需要单独配置 Azure 文件共享的目录或文件级权限。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>为 Azure 文件配置目录或文件级权限

Azure 文件共享在目录和文件级别（包括根目录）强制实施标准 Windows 文件权限。 SMB 和 REST 都支持目录或文件级权限的配置。 从 VM 装载目标文件共享，并使用 Windows 文件资源管理器、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)命令配置权限。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>使用存储帐户密钥获取超级用户权限

具有存储帐户密钥的用户可以访问具有超级用户权限的 Azure 文件共享。 超级用户权限绕过所有访问控制限制。

> [!IMPORTANT]
> 我们推荐的安全最佳实践是避免共享存储帐户密钥，并尽可能利用基于身份的身份验证。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>将数据导入 Azure 文件共享时保留目录和文件 ACL

Azure 文件支持将数据复制到 Azure 文件共享时保留目录或文件级别 ACL。 您可以使用 Azure 文件同步或公共文件移动工具集将目录或文件中的 ACL 复制到 Azure 文件共享。 例如，可以将[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)与`/copy:s`标志一起复制数据以及 ACL 复制到 Azure 文件共享。 默认情况下，ACL 将保留，您不需要在存储帐户上启用基于身份的身份验证来保留 ACL。

## <a name="pricing"></a>定价
存储帐户上通过 SMB 启用基于身份的身份验证不会收取额外的服务费。 有关定价的详细信息，请参阅[Azure 文件定价](https://azure.microsoft.com/pricing/details/storage/files/)和[Azure AD 域服务定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

## <a name="next-steps"></a>后续步骤
有关通过 SMB 进行 Azure 文件和基于标识的身份验证的详细信息，请参阅以下资源：

- [规划 Azure 文件部署](storage-files-planning.md)
- [通过 SMB 为 Azure 文件共享启用本地活动目录域服务身份验证](storage-files-identity-auth-active-directory-enable.md)
- [在 Azure 文件上启用 Azure 活动目录域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常见问题解答](storage-files-faq.md)
