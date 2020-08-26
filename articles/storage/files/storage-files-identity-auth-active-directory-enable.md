---
title: 概述-本地 AD DS 对 Azure 文件共享的身份验证
description: 了解 Active Directory 域服务 (AD DS Azure 文件共享) 身份验证。 本文介绍了支持方案和可用性，并说明了在 AD DS 和 Azure active directory 之间的权限如何工作。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/12/2020
ms.author: rogarana
ms.openlocfilehash: 3faa86fe67e3f0a208bf42dc3e49de8335b25c95
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272325"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>概述-本地 Active Directory 域服务针对 Azure 文件共享的 SMB 进行身份验证

[Azure 文件](storage-files-introduction.md)  通过以下两种类型的域服务，支持通过服务器消息块进行基于身份的身份验证 (SMB) ：本地 Active Directory 域服务 (AD DS) Azure Active Directory (Azure AD) 和域服务。 强烈建议查看 [其工作原理部分](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) ，为身份验证选择适当的域服务。 根据所选的域服务，设置会有所不同。 这些文章重点介绍如何启用和配置本地 AD DS 以通过 Azure 文件共享进行身份验证。

如果你不熟悉 Azure 文件共享，我们建议在阅读以下文章之前阅读我们的 [规划指南](storage-files-planning.md) 。

## <a name="supported-scenarios-and-restrictions"></a>支持的方案和限制

- 用于本地 Azure 文件 AD DS 身份验证的 AD DS 标识必须同步到 Azure AD。 密码哈希同步是可选的。 
- 支持 Azure 文件同步管理的 Azure 文件共享。
- 支持通过具有 RC4-HMAC 加密的 AD 进行 Kerberos 身份验证。 目前尚不支持 AES Kerberos 加密。
- 支持单一登录体验。
- 仅支持在版本低于 Windows 7 或 Windows Server 2008 R2 的操作系统版本上运行的客户端。
- 仅支持将存储帐户注册到的 AD 林。 默认情况下，只能使用单个林中的 AD DS 凭据访问 Azure 文件共享。 如果需要从其他林访问 Azure 文件共享，请确保配置了正确的林信任，有关详细信息，请参阅 [常见问题解答](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) 。
- 不支持对 AD DS 中创建的计算机帐户进行身份验证。 

通过 SMB 为 Azure 文件共享启用 AD DS 时，AD DS 联接的计算机可以使用现有 AD DS 凭据装载 Azure 文件共享。 此功能可以通过本地的计算机托管 AD DS，也可以在 Azure 中托管。

> [!NOTE]
> 为了帮助你在一些常见用例中设置 Azure 文件 AD 身份验证，我们发布了两个视频，其中包含以下方案的分步指导：
> - [将本地文件服务器替换为 Azure 文件 (包括在文件和 AD 身份验证的专用链接上安装) ](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [使用 Azure 文件作为 Windows 虚拟机的配置文件容器 (包括在 AD 身份验证和 FsLogix 配置上安装) ](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>先决条件 

在为 Azure 文件共享启用 AD DS 身份验证之前，请确保已完成以下先决条件： 

- 选择或创建 [AD DS 环境](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ，并将 [其同步到 Azure AD Connect Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 。 

    可以在新的或现有的本地 AD DS 环境中启用该功能。 用于访问的标识必须同步到 Azure AD。 要访问的 Azure AD 租户和文件共享必须与相同的订阅相关联。

- 域-将本地计算机或 Azure VM 加入本地 AD DS。 有关如何加入域的信息，请参阅将 [计算机加入域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。

    如果你的计算机未加入到 AD DS 的域，则如果你的计算机具有 AD 域控制器的线路，你仍可以利用 AD 凭据进行身份验证。

- 选择或创建一个 Azure 存储帐户。  为了获得最佳性能，我们建议你将存储帐户部署到计划从中访问共享的客户端所在的同一区域。 然后，将 [Azure 文件共享装载](storage-how-to-use-files-windows.md) 到存储帐户密钥。 装载存储帐户密钥将验证连接性。

    请确保尚未为包含文件共享的存储帐户配置 Azure AD DS 身份验证。 如果在存储帐户上启用了 Azure 文件 Azure AD DS 身份验证，则需要在更改后将其禁用，才能使用本地 AD DS。 这意味着，将需要重新配置在 Azure AD DS 环境中配置的现有 Acl，以实现适当的权限。

    如果在连接到 Azure 文件时遇到问题，请参阅在 [Windows 上为 Azure 文件安装错误发布的疑难解答工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我们还提供了有关在阻止端口445时解决方案的 [指南](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 。 

- 在启用和配置 Azure 文件共享 AD DS 身份验证之前，请进行任何相关的网络配置。 有关详细信息，请参阅 [Azure 文件网络注意事项](storage-files-networking-overview.md) 。

## <a name="regional-availability"></a>区域可用性

在 [所有 Azure 公共区域和 Gov 区域](https://azure.microsoft.com/global-infrastructure/locations/)中都提供了使用 AD DS 的 azure 文件身份验证。

## <a name="overview"></a>概述

如果你计划启用文件共享上的任何网络配置，我们建议你阅读 [网络注意事项](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) 文章并完成相关配置，然后再启用 AD DS 身份验证。

如果为 Azure 文件共享启用 AD DS 身份验证，则可以通过本地 AD DS 凭据对 Azure 文件共享进行身份验证。 此外，它还允许您更好地管理权限，以允许粒度访问控制。 这样做需要将标识从本地 AD DS 同步到 AD connect Azure AD。 使用本地 AD DS 凭据管理文件/共享级别访问时，可以使用同步到 Azure AD 的标识控制共享级别访问。

接下来，请按照以下步骤设置 Azure 文件以进行 AD DS 身份验证： 

1. [第一部分：在存储帐户上启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)

1. [第二部分：将共享的访问权限分配给 Azure AD 标识 (用户、组或服务主体) 与目标 AD 标识同步](storage-files-identity-ad-ds-assign-permissions.md)

1. [第三部分：通过 SMB 为目录和文件配置 Windows Acl](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [第四部分：将 Azure 文件共享装载到加入到 AD DS 的 VM](storage-files-identity-ad-ds-mount-file-share.md)

1. [更新中的存储帐户标识的密码 AD DS](storage-files-identity-ad-ds-update-password.md)

下图说明了用于启用对 Azure 文件共享的 SMB 进行 Azure AD 身份验证的端到端工作流。 

![文件广告工作流关系图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

用于访问 Azure 文件共享的标识必须同步到 Azure AD，才能通过 [AZURE RBAC) 模型 (通过 azure 基于角色的访问控制 ](../../role-based-access-control/overview.md) 来强制执行共享级别文件权限。 将保留并强制执行从现有文件服务器中执行的文件/目录上的[Windows 样式 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 。 这提供了与企业 AD DS 环境的无缝集成。 将本地文件服务器替换为 Azure 文件共享时，现有用户可以通过单一登录体验从当前客户端访问 Azure 文件共享，而无需更改所使用的凭据。  

## <a name="next-steps"></a>后续步骤

若要为 Azure 文件共享启用本地 AD DS 身份验证，请继续阅读以下文章：

[第一部分：为帐户启用 AD DS 身份验证](storage-files-identity-ad-ds-enable.md)
