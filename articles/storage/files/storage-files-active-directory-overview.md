---
title: 概述-Azure 文件基于标识的授权
description: Azure 文件通过 SMB （服务器消息块） Azure Active Directory 域服务（AD DS）和 Active Directory 支持基于身份的身份验证。 然后，已加入域的 Windows 虚拟机 (VM) 可使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598810"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>适用于 SMB 访问的 Azure 文件基于标识的身份验证支持概述
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

若要了解如何为 Azure 文件共享启用 AD 身份验证，请参阅[通过 SMB 为 azure 文件共享启用 Active Directory 身份验证](storage-files-identity-auth-active-directory-enable.md)。

若要了解如何为 Azure 文件共享启用 Azure AD DS 身份验证，请参阅[在 Azure 文件上启用 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="glossary"></a>术语表 
了解与 Azure 文件共享的 SMB 的 Azure AD 域服务身份验证相关的一些关键术语，这会很有帮助。

-   **Kerberos 身份验证**

    Kerberos 是一种身份验证协议，用于验证用户或主机的标识。 有关 Kerberos 的详细信息，请参阅 [Kerberos 身份验证概述](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **服务器消息块 (SMB) 协议**

    SMB 是行业标准网络文件共享协议。 也称为通用 Internet 文件系统或 CIFS。 有关 SMB 的详细信息，请参阅 [Microsoft SMB 协议和 CIFS协议概述](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。 Azure AD 允许已加入域的 Windows 虚拟机（Vm）使用 Azure AD 凭据访问 Azure 文件共享。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD 域服务（Azure AD DS）**

    Azure AD 域服务（GA）提供了托管域服务，例如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证。 这些服务与 Windows Server Active Directory 完全兼容。 有关详细信息，请参阅 [Azure Active Directory (AD) 域服务](../../active-directory-domain-services/overview.md)。

- **Active Directory 域服务（AD DS，也称为 AD）**

    Active directory （AD）（预览版）提供存储目录数据的方法，同时使其可供网络用户和管理员使用。 通过登录身份验证和对目录中对象的访问控制，安全与 Active Directory 集成。 通过单一网络登录，管理员可以管理其整个网络中的目录数据和组织，授权网络用户可以访问网络上任何位置的资源。 AD 通常被本地企业采用，并使用 AD 凭据作为访问控制的标识。 有关详细信息，请参阅[Active Directory 域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

-   **Azure 基于角色的访问控制 (RBAC)**

    Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可通过向用户授予执行其作业所需的最少权限来管理对资源的访问权限。 有关 RBAC 的详细信息，请参阅[什么是 Azure 中基于角色的访问控制（RBAC）？](../../role-based-access-control/overview.md)。

## <a name="common-use-cases"></a>常见用例

在以下用例中，最大程度地利用了基于身份的身份验证和对 Azure 文件上的 Windows Acl 的支持：

### <a name="replace-on-premises-file-servers"></a>替换本地文件服务器

弃用和替换分散的本地文件服务器是每个企业在 IT 现代化旅程中遇到的一个常见问题。 当你可以将数据迁移到 Azure 文件时，通过 AD （预览版）进行身份验证的 Azure 文件共享最适合。 完整的迁移可让你充分利用高可用性和可伸缩性优势，同时最大限度地减少客户端更改，尤其是复杂的 AD 域基础结构。 它向最终用户提供无缝的迁移体验，因此他们可以使用现有的加入域的计算机继续使用相同的凭据访问其数据。

### <a name="lift-and-shift-applications-to-azure"></a>将应用程序直接迁移到 Azure

将应用程序 "直接迁移" 到云时，需要为数据保留相同的身份验证模型。 随着我们将基于标识的访问控制体验扩展到 Azure 文件共享，无需将应用程序更改为新式身份验证方法并加速云采用。 Azure 文件共享提供了与 Azure AD DS （GA）或 AD （预览版）集成以进行身份验证的选项。 如果你的计划为100% 云的云，并且最大限度地减少了管理云基础结构的工作量，则 Azure AD DS 将更适合作为完全托管的域服务。 如果需要与 AD DS （GA）功能完全兼容，你可能需要考虑通过 Vm 上的自承载域控制器将 AD 环境扩展到云。 无论采用哪种方式，我们都可以灵活地选择适合你的业务需求的域服务。

### <a name="backup-and-disaster-recovery-dr"></a>备份和灾难恢复（DR）

如果要将主文件存储在本地，则 Azure 文件共享可以作为备份或灾难恢复的理想存储，以改善业务连续性。 可以使用 Azure 文件共享从现有文件服务器备份数据，同时保留 Windows Dacl。 对于 DR 方案，可以配置身份验证选项，以支持在故障转移时进行适当的访问控制强制。

## <a name="supported-scenarios"></a>支持的方案

下表总结了 Azure AD DS （GA）和 AD （预览版）支持的 Azure 文件共享身份验证方案。 建议为客户端环境选择采用的域服务，以便与 Azure 文件集成。 如果已在本地或 Azure 中设置了 AD （预览版），并且设备已加入 AD，则应选择使用 AD （预览版）进行 Azure 文件共享身份验证。 同样，如果已采用 Azure AD DS （GA），则应将其用于 Azure 文件共享身份验证。


|Azure AD DS （GA）身份验证  |AD （预览版）身份验证  |
|---------|---------|
|已加入 Azure AD DS 域的 Windows 计算机可以通过 SMB Azure AD 凭据访问 Azure 文件共享。     |已加入 AD 域的 Windows 计算机可以使用通过 SMB 同步到 Azure AD 的 AD 凭据来访问 Azure 文件共享。         |

### <a name="unsupported-scenarios"></a>不支持的方案

- Azure AD DS （GA）和 AD （预览版）身份验证不支持针对计算机帐户进行身份验证。 可以考虑改为使用服务登录帐户。
- Azure AD DS （GA）身份验证不支持对已加入云 Azure AD 云设备进行身份验证。

## <a name="advantages-of-identity-based-authentication"></a>基于标识的身份验证的优点
使用共享密钥身份验证时，Azure 文件的基于标识的身份验证具有多项优势：

-   **使用 AD 和 Azure AD DS 将传统的基于标识的文件共享访问体验扩展到云**  
    如果你计划将应用程序 "直接迁移" 到云，并将传统文件服务器替换为 Azure 文件共享，则可能希望你的应用程序使用 AD 或 Azure AD 凭据进行身份验证，以访问文件数据。 Azure 文件支持使用 AD 或 Azure AD 凭据从 AD 或 Azure AD DS 已加入域的 Vm 中通过 SMB 访问 Azure 文件共享。

-   **对 Azure 文件共享强制实施粒度访问控制**  
    你可以在共享、目录或文件级别向特定标识授予权限。 例如，假设多个团队使用一个 Azure 文件共享进行项目协作。 你可以向所有团队授予访问非敏感目录的权限，而仅授权财务团队访问含敏感财务数据的目录。 

-   **将 Windows Acl （也称为 NTFS）与数据一起备份**  
    可以使用 Azure 文件共享来备份现有的本地文件共享。 当你将文件共享备份到 SMB 上的 Azure 文件共享时，Azure 文件将保留你的 Acl 和数据。

## <a name="how-it-works"></a>工作原理
Azure 文件共享支持 kerberos 身份验证，以便与 Azure AD DS （GA）或 AD （预览版）集成。 必须先设置域环境，然后才能在 Azure 文件共享上启用身份验证。 对于 Azure AD DS （GA）身份验证，你应该启用 Azure AD 域服务和域加入你计划用于访问文件数据的 Vm。 已加入域的 VM 必须与 Azure AD 域服务位于同一虚拟网络（VNET）中。 同样，对于 AD （预览版）身份验证，需要设置 Active Directory 域控制器和域加入计算机或 Vm。

当与在 VM 上运行的应用程序关联的标识尝试访问 Azure 文件共享中的数据时，请求将发送到 Azure AD 域服务对标识进行身份验证。 如果身份验证成功，Azure AD 域服务会返回一个 Kerberos 令牌。 应用程序发送包含 Kerberos 令牌的请求，而 Azure 文件共享使用该令牌对请求进行授权。 Azure 文件共享仅接收令牌，不会保留 Azure AD 凭据。 AD 身份验证的工作方式类似，其中 AD 提供了 Kerberos 令牌。

![屏幕截图显示了通过 SMB 进行 Azure AD 身份验证的关系图](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>启用基于标识的身份验证

你可以在新的和现有的存储帐户上使用 Azure 文件共享的 Azure AD DS （GA）或 AD （预览版）来启用基于身份的身份验证。 在存储帐户上只能使用一个域服务进行文件访问身份验证，这种身份验证适用于帐户中的所有文件共享。 有关设置你的文件共享以便使用 Azure AD DS （GA）进行身份验证的详细分步指南在我们的文章中，为[Azure 文件启用 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)和 AD （预览版）的指南，[启用对 azure 文件共享的 SMB 的 Active Directory 身份验证](storage-files-identity-auth-active-directory-enable.md)。

### <a name="configure-share-level-permissions-for-azure-files"></a>为 Azure 文件配置共享级别权限

启用 Azure AD DS （GA）或 AD （预览版）身份验证后，可以使用内置 RBAC 角色或为 Azure AD 标识配置自定义角色，并将访问权限分配给存储帐户中的任何文件共享。 已分配的权限允许授予的标识仅获取对该共享的访问权限，而不是其他任何内容，甚至是根目录。 你仍需要分别为 Azure 文件共享配置目录或文件级权限。

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>配置 Azure 文件的目录或文件级权限

Azure 文件共享在目录和文件级别（包括根目录）强制实施标准 Windows 文件权限。 SMB 和 REST 都支持目录或文件级权限的配置。 从 VM 装载目标文件共享并使用 Windows 文件资源管理器、Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)命令配置权限。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>使用存储帐户密钥获取超级用户权限

拥有存储帐户密钥的用户可以使用超级用户权限访问 Azure 文件共享。 超级用户权限会绕过所有访问控制限制。

> [!IMPORTANT]
> 我们建议的最佳安全做法是避免共享你的存储帐户密钥，并尽可能利用基于身份的身份验证。

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>将数据导入 Azure 文件共享时保留目录和文件 Acl

将数据复制到 Azure 文件共享时，Azure 文件支持保留目录或文件级 Acl。 你可以使用 Azure 文件同步或常见文件移动工具集将目录或文件上的 Acl 复制到 Azure 文件共享。 例如，可以将[robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)与 `/copy:s` 标志一起使用，以便将数据和 acl 复制到 Azure 文件共享。 默认情况下，将保留 Acl，无需在存储帐户上启用基于标识的身份验证来保留 Acl。

## <a name="pricing"></a>定价
在存储帐户上通过 SMB 启用基于身份的身份验证不会产生额外的服务费用。 有关定价的详细信息，请参阅[Azure 文件定价](https://azure.microsoft.com/pricing/details/storage/files/)和[Azure AD 域服务定价](https://azure.microsoft.com/pricing/details/active-directory-ds/)页（如果你要查找 AAD DS 信息）。

## <a name="next-steps"></a>后续步骤
有关 Azure 文件的详细信息以及基于 SMB 的基于身份的身份验证，请参阅以下资源：

- [规划 Azure 文件部署](storage-files-planning.md)
- [启用对 Azure 文件共享的 SMB 的 Active Directory 身份验证](storage-files-identity-auth-active-directory-enable.md)
- [启用 Azure 文件上 Azure Active Directory 域服务身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [常见问题](storage-files-faq.md)
