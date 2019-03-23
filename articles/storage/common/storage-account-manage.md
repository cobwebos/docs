---
title: 在 Azure 门户中管理存储帐户设置 - Azure 存储| Microsoft Docs
description: 了解如何在 Azure 门户中管理存储帐户设置，包括配置访问控制设置、重新生成帐户访问密钥、更改访问层或修改帐户使用的复制类型。 还将了解如何在门户中删除存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/05/2019
ms.author: tamram
ms.openlocfilehash: fa574558afeec5a7706482a142c0187e6a34bdb3
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370380"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>在 Azure 门户中管理存储帐户设置

[Azure 门户](https://portal.azure.com)中提供了各种存储帐户设置。 本文介绍其中一些设置及其使用方式。

## <a name="access-control"></a>访问控制

Azure 存储 Blob 存储和队列存储通过基于角色的访问控制 (RBAC) 支持使用 Azure Active Directory 进行身份验证。 有关使用 Azure AD 的身份验证的详细信息，请参阅[进行身份验证访问 Azure blob 和队列使用 Azure Active Directory](storage-auth-aad.md)。

Azure 门户中的“访问控制”设置提供了一种将 RBAC 角色分配给用户、组、服务主体和托管标识的简单方法。 有关分配 RBAC 角色的详细信息，请参阅[blob 和队列数据，使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

> [!NOTE]
> 与其他授权方式相比，使用 Azure AD 凭据对用户或应用程序进行身份验证可以提供优越的安全性和易用性。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。 

## <a name="tags"></a>标记

Azure 存储支持 Azure 资源管理器标记，以使用自定义分类组织 Azure 资源。 可将标记应用于存储帐户，以便以逻辑方式在订阅中对其进行分组。 

对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。

有关详细信息，请参阅[使用标记来组织 Azure 资源](../../azure-resource-manager/resource-group-using-tags.md)。

## <a name="access-keys"></a>访问密钥

创建存储帐户时，Azure 会生成两个 512 位的存储帐户访问密钥。 这些密钥可用于授权通过共享密钥访问存储帐户。 可以在不中断应用程序的情况下轮换使用和重新生成密钥，Microsoft 建议定期执行此操作。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>查看和复制访问密钥

若要查看存储帐户凭据，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
4. 找到“key1”下面的“密钥”值，单击“复制”按钮复制该帐户密钥。
5. 或者，可复制整个连接字符串。 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。

    ![显示如何在 Azure 门户中查看访问密钥的屏幕截图](media/storage-manage-account/portal-connection-string.png)

### <a name="regenerate-access-keys"></a>重新生成访问密钥

Microsoft 建议定期重新生成访问密钥，这有助于确保存储帐户的安全。 分配了两个访问密钥，以便轮换使用。 轮换使用密钥时，确保整个过程中应用程序始终可访问 Azure 存储。 

> [!WARNING]
> 重新生成访问密钥会影响依赖于存储帐户密钥的所有应用程序或 Azure 服务。 必须将使用帐户密钥访问存储帐户的所有客户端更新为使用新密钥，包括媒体服务、云、桌面和移动应用程序以及用于 Azure 存储的图形用户界面应用程序（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）。 

按照此流程轮换使用存储帐户密钥：

1. 更新应用程序代码中的连接字符串以使用辅助密钥。
2. 为存储帐户重新生成主访问密钥。 在 Azure 门户中的“访问密钥”边栏选项卡上，单击“重新生成密钥 1”，并单击“是”以确认要生成新密钥。
3. 更新代码中的连接字符串以引用新的主访问密钥。
4. 以相同方式重新生成辅助访问密钥。

## <a name="account-configuration"></a>帐户配置

创建存储帐户后，可修改其配置。 例如，可更改数据的复制方式，或将帐户的访问层从热访问层更改为冷访问层。 在 [Azure 门户](https://portal.azure.com)中，导航到存储帐户，然后找到并单击“设置”下的“配置”以查看和/或更改帐户配置。

更改存储帐户配置可能会导致成本增加。 有关更多详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)页。

## <a name="delete-a-storage-account"></a>删除存储帐户
要删除不再使用的存储帐户，请在 [Azure 门户](https://portal.azure.com)中导航到该存储帐户，并单击“删除”。 删除存储帐户将删除整个帐户，包括该帐户中的所有数据。

> [!WARNING]
> 无法恢复已删除的存储帐户，也无法检索删除之前该存储帐户包含的任何内容。 删除帐户前请务必备份要保存的任何内容。 对于帐户中的任务资源也是如此 — 一旦你删除了一个 Blob、表、队列或文件，则它会被永久删除。
> 

如果尝试删除与 Azure 虚拟机关联的存储帐户，则会显示一条错误消息，指出存储帐户仍在使用。 有关如何排查此错误的帮助，请参阅[排查删除存储帐户时的错误](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-quickstart-create-account.md)
